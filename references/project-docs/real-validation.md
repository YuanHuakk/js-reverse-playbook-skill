# Real MCP Validation

这份文档定义 **真实 MCP 链路验收**。它和 `pnpm test` 分工不同：

- `pnpm test` 验证单元、属性、集成和回归逻辑。
- `pnpm validate:*` 验证真实浏览器、真实 MCP server、真实页面和真实 CDP/Patchright 兼容链路。

不要用单元测试结果替代真实 MCP 验收。

## 前置条件

先启动一个带 DevTools 端口的浏览器：

```bash
chrome --remote-debugging-port=9222
```

Windows 可按本机 Chrome 路径启动，例如：

```powershell
& "C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222
```

如端口不是 `9222`，传入：

```bash
pnpm run validate:real -- --browser-url=http://127.0.0.1:9333
```

## 验收命令

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

每个脚本都会：

1. 启动本地动态分析靶场。
2. 通过 MCP SDK 拉起 `build/src/entry/index.js`。
3. 连接指定浏览器 DevTools endpoint。
4. 逐项调用 MCP tools。
5. 输出 `OK` / `BAD` 和失败摘要。

## 能力矩阵

| 能力                 | 验收脚本             | 覆盖状态     | 说明                                                   |
| -------------------- | -------------------- | ------------ | ------------------------------------------------------ |
| Browser health       | `validate:mcp`       | 已真实验证   | `check_browser_health`                                 |
| Page navigation      | `validate:mcp`       | 已真实验证   | `navigate_page` / `list_pages`                         |
| DOM query            | `validate:mcp`       | 已真实验证   | `wait_for_element` / `query_dom` / `get_dom_structure` |
| Interaction          | `validate:mcp`       | 已真实验证   | `click_element` / `type_text`                          |
| Screenshot           | `validate:mcp`       | 已真实验证   | 保存到系统临时目录                                     |
| Performance metrics  | `validate:mcp`       | 已真实验证   | `get_performance_metrics`                              |
| Storage read         | `validate:mcp`       | 已真实验证   | cookies / localStorage / sessionStorage                |
| Stealth presets      | `validate:mcp`       | 已真实验证   | preset、feature、UA、注入                              |
| Session snapshot     | `validate:session`   | 已真实验证   | save / list / dump / load / restore / delete           |
| WebSocket            | `validate:session`   | 已真实验证   | connection list、pattern analyze、frames               |
| Code collection      | `validate:dynamic`   | 已真实验证   | collect / search / priority                            |
| Runtime sampler      | `validate:dynamic`   | 已真实验证   | sampler 入口和高级报告链路                             |
| Target param tracing | `validate:dynamic`   | 已真实验证   | target params、replay hints、risk panel                |
| Analyze target       | `validate:dynamic`   | 已真实验证   | auto replay + network-core hook preset                 |
| Monitor events       | `validate:debugger`  | 已真实验证   | DOM event monitor                                      |
| XHR breakpoint       | `validate:debugger`  | 已真实验证   | pause / info / resume / remove                         |
| Trace function       | `validate:debugger`  | 已真实验证   | logpoint 形式，不暂停                                  |
| Scope snapshot       | `validate:debugger`  | 已真实验证   | debugger pause 后抓局部变量和闭包摘要                  |
| Callframe evaluate   | `validate:debugger`  | 已真实验证   | 停在变量初始化之后再读                                 |
| LLM live provider    | 手动验收             | 依赖外部配置 | 需要 API key / Gemini CLI / 配额                       |
| Performance baseline | `pnpm run test:perf` | 默认不跑     | 需要稳定机器和浏览器环境                               |

## 已知边界

### JS 后注入 Hook

`hook_function` 和 `create_hook` 适合验证可被当前页面上下文重新赋值的函数路径。对以下情况不能当作唯一证据来源：

- 页面已经加载后才注入 hook。
- 目标代码在已编译函数中直接调用未限定的内建函数，例如 `fetch(...)`。
- 目标站点提前缓存了原始函数引用。
- 目标运行在 Worker、Service Worker 或隔离 world 中。

遇到上述情况，优先使用：

- `list_network_requests` / `get_network_request`
- `break_on_xhr`
- `trace_function`
- `get_paused_info`
- `snapshot_scope`
- `collect_code` + `targetParams`

这不是禁用 hook，而是避免把 JS wrapper 当成唯一数据源。

### LLM

LLM 工具分两层验收：

- 单元测试覆盖 prompt、fallback、provider 分支。
- 真实可用性取决于 `.env`、API key、模型权限、Gemini CLI 是否在 PATH 中。

发布前如要验证真实 LLM：

```bash
pnpm start
```

然后通过 MCP client 调用：

```json
{
  "name": "check_llm_health",
  "arguments": { "liveCheck": true }
}
```

## 失败处理

- `Cannot reach browser DevTools endpoint`：先启动 Chrome remote debugging。
- `BAD save_session_state`：优先检查 Patchright cookie context 兼容。
- `click_element Timeout` 且随后 `get_paused_info` 显示 `Execution Paused`：这是断点暂停造成的预期现象，不按点击失败处理。
- `evaluate_on_callframe` 读取 `const` 报 TDZ：先 `step_over` 到变量初始化之后。
- WebSocket 没有 `wsid`：确认靶场页面触发了 `#websocket-flow`，且 collector 已启用 Network domain。

## 发布闸门

本地模式发布或提交前建议至少执行：

```bash
pnpm run check-format
pnpm run typecheck
pnpm test
pnpm run validate:real
```

`validate:real` 需要真实浏览器，因此不放进默认 `release:check`。
