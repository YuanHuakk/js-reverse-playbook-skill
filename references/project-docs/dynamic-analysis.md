# Dynamic Analysis

这份文档是 **动态分析能力的使用协议**。

它不替代静态代码收集，也不承诺自动破解所有参数算法。它回答的是：

- 什么时候应该启用动态分析
- 动态分析会采集哪些证据
- 如何从请求命中走到代码切片、作用域快照和 Node 复现草稿
- 哪些能力默认关闭，必须显式 opt-in

## 适用前提

当任务出现以下情况时，优先使用动态分析：

- 目标参数只在真实浏览器运行时出现
- 静态搜索能找到大量候选代码，但无法判断哪个脚本真正参与请求
- 需要确认 `sign` / `token` / `nonce` / `timestamp` 等字段的生成链路
- 需要把请求证据、运行时调用栈、代码切片和补环境线索串起来
- 需要在授权会话中保留目标参数明文证据

如果只是读取页面脚本、搜索函数名或查看普通源码，先使用基础 `collect_code`，不要默认打开深分析。

## 阶段目标

动态分析把逆向链路拆成四层：

1. **请求层**：哪个请求命中了目标参数
2. **运行时层**：哪个调用栈、哪个 API、哪个异步边界参与了目标行为
3. **代码层**：哪个脚本、哪几行代码最可能参与参数生成
4. **复现层**：如何生成 replay template、Node draft 和补环境 hint

目标不是一次性看懂整站代码，而是把证据收敛到可继续验证的最小范围。

## 核心原则

- `Opt-in-first`
- `Evidence-before-guess`
- `Stack-before-search`
- `Slice-before-rewrite`
- `Redact-by-default`
- `Probe-before-restore`

## 推荐步骤

### Step 1: Collect Runtime Signals

先使用默认低侵入动态信号。

`CodeCollector` 会通过 CDP 订阅：

- `Network.responseReceived`
- `Network.requestWillBeSent`
- `Runtime.exceptionThrown`

这些信号会写入脚本 metadata：

- `runtimeScore`
- `runtimeReasons`
- `priorityScore`

用途是让 `priority` / `top-priority` 排序优先返回真实参与运行时行为的脚本。

### Step 2: Enable Runtime Sampler

当仅靠 CDP 调用栈仍不足时，显式开启 preload sampler：

```json
{
  "url": "https://example.com",
  "smartMode": "priority",
  "enableRuntimeSampler": true
}
```

sampler 覆盖：

- `fetch` / `XMLHttpRequest` / `WebSocket`
- `crypto.subtle`
- `btoa` / `atob`
- `Date.now` / `Math.random`
- `localStorage` / `sessionStorage` / `IndexedDB`
- `document.cookie`
- Promise / timer / event listener / `postMessage` / `MessageChannel`
- Worker / Service Worker 注册边界
- WebAssembly compile / instantiate 边界
- Canvas / WebGL / Audio / fonts 指纹入口
- `eval` / `Function` / `Function.prototype.toString`

记录类型、目标、方法、载荷长度、调用栈和 async 关联；命中 `targetParams` 的字段还会附带明文证据。

### Step 3: Trace Target Params

如果目标是定位参数生成链路，传入 `targetParams`：

```json
{
  "url": "https://example.com",
  "smartMode": "priority",
  "returnMode": "top-priority",
  "targetParams": ["sign", "timestamp", "nonce", "token"]
}
```

目标参数追踪会匹配：

- URL query
- request headers
- JSON body
- form body
- raw body 字段名兜底

命中后输出：

- 参数名和位置
- 请求 method / URL
- 参数值长度
- CDP initiator stack
- 最近 runtime sampler 记录
- replay hint
- 候选 `codeSlices`

### Step 4: Enable Advanced Report

需要完整证据报告时，开启：

```json
{
  "url": "https://example.com",
  "targetParams": ["sign"],
  "enableAdvancedDynamicAnalysis": true
}
```

`advancedDynamicAnalysis` 包含：

- `sampleSummary`
- `replayTemplates`
- `nodeReproductionDrafts`
- `dataFlowHints`
- `dependencyLinks`
- `environmentPatchHints`
- `vmRuntimeHints`
- `sensitiveEvidence`
- `implementedCapabilities`
- `pendingCapabilities`

当前 `pendingCapabilities` 为空。这里的含义是：仓库已经给出对应能力入口、证据模型或 probe 链路；不表示任何站点都能零人工自动还原。

### Step 5: Capture Scope Snapshot

如果需要查看暂停点局部变量、闭包变量或调用帧状态，先用 debugger 工具设置断点，再显式调用：

```json
{
  "name": "snapshot_scope",
  "maxFrames": 3,
  "maxVariablesPerScope": 30,
  "maxStringLength": 120
}
```

`snapshot_scope` 默认会做：

- 字符串长度限制
- scope 变量数量限制
- 非自动暂停，必须由用户显式触发

### Step 6: 目标参数明文证据

命中 `targetParams` 的字段会直接输出明文证据，无需额外开关——逆向时这正是你要的真实值。可选 `maxSensitiveEvidenceLength` 限制单条长度（默认 2000；超出仅截断并标记 `truncated` + `originalLength`，不打码）。

```json
{
  "targetParams": ["sign", "token"],
  "maxSensitiveEvidenceLength": 2000
}
```

只有命中 `targetParams` 的字段会进入：

`advancedDynamicAnalysis.sensitiveEvidence`

不会无差别导出整站 cookie、storage 或请求体。

## 必备输出

一次完整动态分析至少应产出：

- `targetParamMatches`
- `runtimeScore` / `runtimeReasons`
- `codeSlices`
- `replayHint`
- `advancedDynamicAnalysis`，当开启高级报告时

如进入断点分析，还应补充：

- `snapshot_scope` 输出
- 断点位置
- 触发请求
- 是否启用敏感明文证据

## 能力边界

已具备的能力：

- 请求命中和目标参数匹配
- CDP initiator stack 归因
- runtime sampler 调用栈采样
- 代码切片排序
- replay template 生成
- Node reproduction draft 生成
- storage / cookie / crypto / time / random / fingerprint / wasm / worker 边界采样
- asyncId / parentAsyncId 异步链摘要
- `snapshot_scope` 作用域快照
- JSVM / WASM probe hint
- 目标参数明文证据输出

### JS Hook 使用边界

`hook_function` 和 `create_hook` 是辅助抓手，不是网络与参数追踪的唯一来源。

真实浏览器里存在执行世界、函数绑定和提前缓存引用的问题。页面加载后再注入 JS wrapper 时，以下场景可能不会命中：

- 目标函数体已经编译，并直接调用未限定的内建函数，例如 `fetch(...)`
- 页面或框架提前保存了原始函数引用
- 调用发生在 Worker / Service Worker / iframe / isolated world
- 站点主动检测 wrapper、`toString` 或 native code

遇到这类情况，优先使用 CDP 和 debugger 链路：

- `list_network_requests` / `get_network_request`
- `get_request_initiator`
- `break_on_xhr`
- `trace_function`
- `get_paused_info`
- `snapshot_scope`
- `collect_code` + `targetParams`

结论标准：hook 没记录不等于请求没发生。参数定位以 Network、initiator stack、runtime sampler、断点作用域和代码切片共同交叉验证。

需要按站点显式推进的能力：

- 高侵入 hook 伪装
- 防代理 / 防断点 / 时间差对抗
- Worker 内部 sampler 注入
- Service Worker fetch event 细粒度链路
- WASM memory 明文读写恢复
- JSVM opcode 语义还原
- 纯算法最终移植和服务端验收

## 默认安全边界

- 高侵入 hook 具备，但必须显式启用，不进入默认采集链路。
- 命中 `targetParams` 的字段会把明文写入 `advancedDynamicAnalysis.sensitiveEvidence`（逆向所需的真实值），可用 `maxSensitiveEvidenceLength` 控制单条长度。
- 断点暂停和 `snapshot_scope` 具备，但默认不暂停页面执行，必须通过 debugger 工具显式触发。
- 参数算法还原具备定位、切片、采样、scope 快照、Node draft 和环境补丁 hint；不承诺所有站点所有算法都能零人工全自动破解。
- JSVM/WASM 动态辅助具备信号识别和 probe 建议；完整还原走显式 debugger / hook 链路，不放进基础 `collect_code` 默认职责。

## 禁止事项

- 无差别导出整站 cookie / storage / 请求体（只输出命中 `targetParams` 的字段明文）
- 默认启用高侵入 hook
- 默认暂停页面执行
- 把完整站点私有参数算法写进公开 case
- 把 `advancedDynamicAnalysis` 的 hint 当成已完成纯算实现
- 没有固定请求样本就直接进入 Python port

## 完成判据

动态分析完成至少意味着：

- 已定位目标请求和目标参数
- 已关联 runtime stack 或 sampler evidence
- 已生成候选代码切片
- 已说明是否需要敏感明文证据
- 已生成 replay hint 或 replay template
- 如需本地复现，已生成 Node reproduction draft 和环境补丁 hint

若目标进入纯算法提纯阶段，应继续遵循：

`pure-extraction.md`

## 本地靶场

动态分析不要依赖公网样本站点做回归。公网页面会改版、限流、加登录态，也很难稳定覆盖 Worker、Service Worker、WASM、WebSocket 和指纹链路。

仓库内置本地靶场：

`tests/fixtures/dynamic-target/server.ts`

它提供一个可由测试或手工 MCP 会话启动的本地页面，固定暴露以下触发按钮：

- `#runtime-signals`：触发 `Date.now`、`Math.random`、`crypto.subtle`、`btoa` / `atob`、storage、cookie、timer、Promise、MessageChannel、`eval` 和 `Function`
- `#fetch-sign`：发送带 `sign` / `token` / `nonce` / `timestamp` / `qid` 的 `fetch` 请求
- `#xhr-sign`：发送 JSON body 和 header 中带目标参数的 XHR 请求
- `#websocket-flow`：建立 WebSocket，发送文本签名消息和二进制帧
- `#worker-sign`：在 Worker 内部生成 `workerSign`
- `#service-worker-flow`：注册 Service Worker 并触发 fetch event 拦截链路
- `#wasm-flow`：调用内联 WASM `add(i32, i32)`，再把 `wasmValue` 写入请求
- `#fingerprint-flow`：触发 Canvas、WebGL、Audio 指纹入口
- `#anti-debug-flow`：触发 `Function.prototype.toString`、native code 检测和 `debugger`
- `#debug-scope-flow`：在局部变量和闭包变量存在时触发 `debugger`，用于 `snapshot_scope`

靶场 manifest 地址：

```text
/manifest.json
```

靶场事件回放地址：

```text
/events
```

### 推荐 MCP 验收流

1. 启动靶场 server，拿到 `http://127.0.0.1:<port>`。
2. 用 MCP 打开靶场 URL。
3. 调用 `collect_code`，传入：

```json
{
  "url": "http://127.0.0.1:<port>",
  "smartMode": "priority",
  "includeExternal": true,
  "includeInline": true,
  "enableRuntimeSampler": true,
  "enableAdvancedDynamicAnalysis": true,
  "targetParams": ["sign", "token", "nonce", "timestamp", "qid", "workerSign", "wasmValue"]
}
```

4. 用 `autoReplayActions` 或 `click_element` 依次触发上面的按钮。
5. 检查以下 MCP 输出：

- `targetParamMatches` 至少命中 `sign`、`token`、`nonce`、`timestamp`、`qid`
- runtime sampler 至少包含 fetch、XHR、WebSocket、crypto、timer、random、storage、cookie、worker、service-worker、wasm、fingerprint、eval 信号
- `list_websocket_connections` 能看到 `/ws`
- `analyze_websocket_messages` 能把文本签名消息和二进制帧分组
- `snapshot_scope` 在 `#debug-scope-flow` 暂停后能看到局部变量名和闭包变量摘要
- `advancedDynamicAnalysis.sensitiveEvidence` 只在显式 opt-in 时输出命中的目标参数明文证据
- `replayTemplates` 和 `nodeReproductionDrafts` 至少包含 fetch / XHR 复现草稿

### 靶场回归测试

基础靶场可用性测试：

```bash
pnpm run build
node --require ./build/tests/setup.js --no-warnings=ExperimentalWarning --test-reporter spec --test-force-exit --test "build/tests/integration/dynamic-analysis-target.integration.test.js"
```

这组测试验证 server、manifest、API 事件记录和 WebSocket 帧处理。完整 MCP 浏览器链路需要在真实浏览器和 MCP server 启动后按“推荐 MCP 验收流”执行。

### 真实 MCP 验收脚本

仓库提供可复跑的真实链路验收脚本：

```bash
pnpm run validate:mcp
pnpm run validate:session
pnpm run validate:dynamic
pnpm run validate:debugger
```

一键执行：

```bash
pnpm run validate:real
```

运行前需要先启动 Chrome DevTools endpoint，例如 `http://127.0.0.1:9222`。完整说明见：

`real-validation.md`

## 与其他文档的关系

- 总阶段协议：`reverse-workflow.md`
- 补环境规范：`env-patching.md`
- 产物约束：`reverse-artifacts.md`
- 纯算法提纯：`pure-extraction.md`
- 任务入口：`reverse-bootstrap.md`
- 真实 MCP 验收：`real-validation.md`
