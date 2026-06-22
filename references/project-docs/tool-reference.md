<!-- AUTO GENERATED DO NOT EDIT - run 'pnpm run docs' to update-->

# Chrome DevTools MCP Tool Reference

> 快速按逆向目标查工具，请先看：[`reverse-task-index.md`](./reverse-task-index.md)

- **[Navigation automation](#navigation-automation)** (18 tools)
  - [`check_browser_health`](#check_browser_health)
  - [`click_element`](#click_element)
  - [`delete_session_state`](#delete_session_state)
  - [`dump_session_state`](#dump_session_state)
  - [`find_clickable_elements`](#find_clickable_elements)
  - [`get_dom_structure`](#get_dom_structure)
  - [`get_performance_metrics`](#get_performance_metrics)
  - [`list_pages`](#list_pages)
  - [`list_session_states`](#list_session_states)
  - [`load_session_state`](#load_session_state)
  - [`navigate_page`](#navigate_page)
  - [`new_page`](#new_page)
  - [`query_dom`](#query_dom)
  - [`restore_session_state`](#restore_session_state)
  - [`save_session_state`](#save_session_state)
  - [`select_page`](#select_page)
  - [`type_text`](#type_text)
  - [`wait_for_element`](#wait_for_element)
- **[Network](#network)** (7 tools)
  - [`analyze_websocket_messages`](#analyze_websocket_messages)
  - [`get_network_request`](#get_network_request)
  - [`get_websocket_message`](#get_websocket_message)
  - [`get_websocket_messages`](#get_websocket_messages)
  - [`list_network_requests`](#list_network_requests)
  - [`list_websocket_connections`](#list_websocket_connections)
  - [`replay_request`](#replay_request)
- **[Debugging](#debugging)** (5 tools)
  - [`evaluate_script`](#evaluate_script)
  - [`get_console_message`](#get_console_message)
  - [`inject_preload_script`](#inject_preload_script)
  - [`list_console_messages`](#list_console_messages)
  - [`take_screenshot`](#take_screenshot)
- **[JS Reverse Engineering](#js-reverse-engineering)** (47 tools)
  - [`analyze_target`](#analyze_target)
  - [`break_on_xhr`](#break_on_xhr)
  - [`check_llm_health`](#check_llm_health)
  - [`collect_code`](#collect_code)
  - [`collection_diff`](#collection_diff)
  - [`create_hook`](#create_hook)
  - [`deobfuscate_code`](#deobfuscate_code)
  - [`detect_crypto`](#detect_crypto)
  - [`evaluate_on_callframe`](#evaluate_on_callframe)
  - [`export_session_report`](#export_session_report)
  - [`find_in_script`](#find_in_script)
  - [`get_hook_data`](#get_hook_data)
  - [`get_paused_info`](#get_paused_info)
  - [`get_request_initiator`](#get_request_initiator)
  - [`get_script_source`](#get_script_source)
  - [`get_storage`](#get_storage)
  - [`hook_function`](#hook_function)
  - [`inject_hook`](#inject_hook)
  - [`inject_stealth`](#inject_stealth)
  - [`inspect_object`](#inspect_object)
  - [`list_breakpoints`](#list_breakpoints)
  - [`list_hooks`](#list_hooks)
  - [`list_scripts`](#list_scripts)
  - [`list_stealth_features`](#list_stealth_features)
  - [`list_stealth_presets`](#list_stealth_presets)
  - [`monitor_events`](#monitor_events)
  - [`pause`](#pause)
  - [`record_reverse_evidence`](#record_reverse_evidence)
  - [`remove_breakpoint`](#remove_breakpoint)
  - [`remove_hook`](#remove_hook)
  - [`remove_xhr_breakpoint`](#remove_xhr_breakpoint)
  - [`resume`](#resume)
  - [`risk_panel`](#risk_panel)
  - [`search_in_scripts`](#search_in_scripts)
  - [`search_in_sources`](#search_in_sources)
  - [`set_breakpoint`](#set_breakpoint)
  - [`set_breakpoint_on_text`](#set_breakpoint_on_text)
  - [`set_user_agent`](#set_user_agent)
  - [`snapshot_scope`](#snapshot_scope)
  - [`step_into`](#step_into)
  - [`step_out`](#step_out)
  - [`step_over`](#step_over)
  - [`stop_monitor`](#stop_monitor)
  - [`summarize_code`](#summarize_code)
  - [`trace_function`](#trace_function)
  - [`understand_code`](#understand_code)
  - [`unhook_function`](#unhook_function)

## Navigation automation

### `check_browser_health`

**Description:** 在运行逆向工作流之前，检查浏览器连接状态和活动页面就绪情况。

### `click_element`

**Description:** 通过选择器点击一个元素。

**Parameters:**

- `selector`

### `delete_session_state`

**Description:** 按 sessionId 删除一个内存中的会话快照。

**Parameters:**

- `sessionId`

### `dump_session_state`

**Description:** 将已保存的会话快照导出为 JSON，可选择写入文件。

**Parameters:**

- `sessionId`
- `path`
- `pretty`
- `encrypt`

### `find_clickable_elements`

**Description:** 查找可点击的按钮/链接，可选按文本过滤。

**Parameters:**

- `filterText`

### `get_dom_structure`

**Description:** 获取当前页面的 DOM 树结构。

**Parameters:**

- `maxDepth`
- `includeText`

### `get_performance_metrics`

**Description:** 通过 Performance API 获取页面性能指标。

### `list_pages`

**Description:** 获取浏览器中已打开的页面列表。

### `list_session_states`

**Description:** 列出内存中所有已保存的会话快照。

### `load_session_state`

**Description:** 从 JSON 字符串或文件加载会话快照到内存。

**Parameters:**

- `sessionId`
- `path`
- `snapshotJson`
- `overwrite`

### `navigate_page`

**Description:** 将当前选中的页面导航到某个 URL，或执行后退/前进/重新加载操作。等待 DOMContentLoaded 事件（而非整页加载完成）。默认超时为 10 秒。

**Parameters:**

- `type`
- `url`
- `ignoreCache`
- `timeout`

### `new_page`

**Description:** 创建一个新页面并导航到指定 URL。等待 DOMContentLoaded 事件（而非整页加载完成）。默认超时为 10 秒。

**Parameters:**

- `url`
- `timeout`

### `query_dom`

**Description:** 通过 CSS 选择器查询一个或多个元素。

**Parameters:**

- `selector`
- `all`
- `limit`

### `restore_session_state`

**Description:** 将先前保存的会话快照恢复到当前页面。

**Parameters:**

- `sessionId`
- `navigateToSavedUrl`
- `clearStorageBeforeRestore`

### `save_session_state`

**Description:** 将当前页面的会话状态（cookies/localStorage/sessionStorage）保存为内存快照。

**Parameters:**

- `sessionId`
- `includeCookies`
- `includeLocalStorage`
- `includeSessionStorage`

### `select_page`

**Description:** 选择一个页面作为后续工具调用的上下文。

**Parameters:**

- `pageIdx`

### `type_text`

**Description:** 向输入元素中输入文本。

**Parameters:**

- `selector`
- `text`
- `delay`

### `wait_for_element`

**Description:** 等待选择器对应的元素出现。

**Parameters:**

- `selector`
- `timeout`

## Network

### `analyze_websocket_messages`

**Description:** 分析 WebSocket 消息并按模式/指纹对其分组。在直播流场景中理解二进制/protobuf 消息类型时尤为关键。为每种消息类型返回统计信息和样本索引。

**Parameters:**

- `wsid`
- `direction`

### `get_network_request`

**Description:** 按可选的 reqid 获取某个网络请求；省略时返回 DevTools 网络面板中当前选中的请求。

**Parameters:**

- `reqid`

### `get_websocket_message`

**Description:** 通过 frame 索引获取单条 WebSocket 消息。请先使用 get_websocket_messages 或 analyze_websocket_messages 找到 frame 索引。

**Parameters:**

- `wsid`
- `frameIndex`

### `get_websocket_messages`

**Description:** 获取某个 WebSocket 连接的消息。重要：对于二进制/protobuf 消息（如直播流），请先使用 analyze_websocket_messages 了解消息类型，再通过 groupId 参数筛选特定类型。默认模式仅显示摘要。

**Parameters:**

- `wsid`
- `direction`
- `groupId`
- `pageSize`
- `pageIdx`
- `show_content`

### `list_network_requests`

**Description:** 列出当前选中页面自上次导航以来的所有网络请求。

**Parameters:**

- `pageSize`
- `pageIdx`
- `resourceTypes`
- `includePreservedRequests`

### `list_websocket_connections`

**Description:** 列出所有 WebSocket 连接。获取到 wsid 后，请先使用 analyze_websocket_messages(wsid) 了解消息模式，再查看单条消息。

**Parameters:**

- `pageSize`
- `pageIdx`
- `urlFilter`
- `includePreservedConnections`

### `replay_request`

**Description:** 在页面上下文中（携带真实 cookie/origin）重新发送一个 HTTP 请求，可选改写 url/method/headers/body，并返回响应。用于端到端验证已复现的签名或参数：以通过 reqid 捕获的请求（来自 list_network_requests）为基础，覆盖其中的签名字段。

**Parameters:**

- `reqid`
- `url`
- `method`
- `headers`
- `body`
- `credentials`
- `maxBodyChars`

## Debugging

### `evaluate_script`

**Description:** 在当前选中的页面内执行一个 JavaScript 函数。返回结果以 JSON 形式给出，
因此返回值必须可被 JSON 序列化。

**Parameters:**

- `function`

### `get_console_message`

**Description:** 按 ID 获取单条控制台消息。可通过调用 list_console_messages 获取全部消息。

**Parameters:**

- `msgid`

### `inject_preload_script`

**Description:** 注册一段 JavaScript 代码，使其在后续文档加载时、页面脚本执行之前运行。可用于预加载脚本的钩子、环境修补以及早期插桩。

**Parameters:**

- `script`

### `list_console_messages`

**Description:** 列出当前选中页面自上次导航以来的所有控制台消息。

**Parameters:**

- `pageSize`
- `pageIdx`
- `types`
- `includePreservedMessages`

### `take_screenshot`

**Description:** 对页面或元素进行截图。

**Parameters:**

- `format`
- `quality`
- `fullPage`
- `filePath`

## JS Reverse Engineering

### `analyze_target`

**Description:** 一键式逆向流程：采集代码、执行安全与加密分析、可选反混淆，并关联 hook 时间线。

**Parameters:**

- `url`
- `topN`
- `useAI`
- `runDeobfuscation`
- `hookPreset`
- `autoInjectHooks`
- `waitAfterHookMs`
- `correlationWindowMs`
- `maxCorrelatedFlows`
- `maxFingerprints`
- `autoReplayActions`
- `collect`

### `break_on_xhr`

**Description:** 设置一个断点，当 XHR/Fetch 请求的 URL 包含指定字符串时触发。

**Parameters:**

- `url`

### `check_llm_health`

**Description:** 检查 LLM 服务商配置，可选发起一次实时对话探测。

**Parameters:**

- `liveCheck`

### `collect_code`

**Description:** 从页面采集 JavaScript 代码，支持智能模式（summary/priority/incremental/full）。

**Parameters:**

- `url`
- `smartMode`
- `returnMode`
- `includeInline`
- `includeExternal`
- `includeDynamic`
- `enableRuntimeSampler`
- `enableAdvancedDynamicAnalysis`
- `maxSensitiveEvidenceLength`
- `targetParams`
- `targetParamSampleWindowMs`
- `maxTotalSize`
- `maxFileSize`
- `pattern`
- `limit`
- `topN`

### `collection_diff`

**Description:** 对比前后两次采集到的文件摘要。

**Parameters:**

- `previous`
- `current`
- `includeUnchanged`

### `create_hook`

**Description:** 推荐：为 function/fetch/xhr/property/cookie/websocket/eval/timer 创建 hook 脚本。hook 在不暂停页面执行的情况下运行，相比断点是监控与拦截的首选方案。

**Parameters:**

- `type`
- `params`
- `description`
- `action`

### `deobfuscate_code`

**Description:** AI 辅助的 JavaScript 反混淆。

**Parameters:**

- `code`
- `aggressive`
- `renameVariables`
- `useAI`

### `detect_crypto`

**Description:** 从 JavaScript 源码中检测加密算法与加密库。

**Parameters:**

- `code`
- `useAI`

### `evaluate_on_callframe`

**Description:** 在暂停时，于指定调用帧的上下文中执行一段 JavaScript 表达式。可借此查看变量并在暂停的作用域中执行代码。

**Parameters:**

- `expression`
- `frameIndex`

### `export_session_report`

**Description:** 将当前逆向会话导出为 JSON 或 Markdown。

**Parameters:**

- `format`
- `includeHookData`

### `find_in_script`

**Description:** 在指定脚本中查找字符串，返回其精确的行/列位置以及周围上下文。非常适合在整段代码挤在一行的压缩文件中设置断点。

**Parameters:**

- `scriptId`
- `query`
- `contextChars`
- `occurrence`
- `caseSensitive`

### `get_hook_data`

**Description:** 获取某个 hook 或全部 hook 捕获的数据。支持原始（raw）视图和用于降噪的摘要（summary）视图。

**Parameters:**

- `hookId`
- `view`
- `maxRecords`

### `get_paused_info`

**Description:** 获取当前暂停状态的信息，包括调用栈、当前位置和作用域变量。命中断点后用它来了解执行上下文。

**Parameters:**

- `includeScopes`
- `maxScopeDepth`

### `get_request_initiator`

**Description:** 获取发起某个网络请求的 JavaScript 调用栈，便于追踪是哪段代码触发了 API 调用。

**Parameters:**

- `requestId`
- `taskId`
- `taskSlug`
- `targetUrl`
- `goal`

### `get_script_source`

**Description:** 通过 scriptId 获取 JavaScript 脚本的源代码。支持按行号范围（普通文件）或字符偏移（压缩成单行的文件）读取。请先用 `list_scripts` 查找 scriptId。

**Parameters:**

- `scriptId`
- `startLine`
- `endLine`
- `offset`
- `length`

### `get_storage`

**Description:** 获取浏览器存储数据，包括 cookies、localStorage 和 sessionStorage。

**Parameters:**

- `type`
- `filter`

### `hook_function`

**Description:** 逆向推荐方式：hook 一个 JavaScript 函数，在不暂停执行的情况下记录它的调用、参数和返回值。在自动化流程中比断点更可靠。监控函数时请将其作为默认方式。

**Parameters:**

- `target`
- `logArgs`
- `logResult`
- `logStack`
- `hookId`

### `inject_hook`

**Description:** 将已有的 hook 注入到当前页面。

**Parameters:**

- `hookId`

### `inject_stealth`

**Description:** 向当前页面注入反检测 stealth 脚本。

**Parameters:**

- `preset`

### `inspect_object`

**Description:** 深度检查一个 JavaScript 对象，展示它的属性、原型链和方法。便于理解对象结构。

**Parameters:**

- `expression`
- `depth`
- `showMethods`
- `showPrototype`

### `list_breakpoints`

**Description:** 列出当前调试会话中所有生效的断点。

### `list_hooks`

**Description:** 列出所有生效的函数 hook。

### `list_scripts`

**Description:** 列出当前页面已加载的所有 JavaScript 脚本，返回 scriptId、URL 和 source map 信息。在设置断点或搜索之前用它来查找脚本。

**Parameters:**

- `filter`

### `list_stealth_features`

**Description:** 列出可用的 stealth 功能开关。

### `list_stealth_presets`

**Description:** 列出可用的 stealth 预设。

### `monitor_events`

**Description:** 监控指定元素或 window 上的 DOM 事件，事件将记录到控制台。

**Parameters:**

- `selector`
- `events`
- `monitorId`
- `taskId`
- `taskSlug`
- `targetUrl`
- `goal`

### `pause`

**Description:** 在当前位置暂停 JavaScript 执行，用于中断正在运行的代码。

### `record_reverse_evidence`

**Description:** 将结构化的逆向证据追加写入任务产物日志。

**Parameters:**

- `taskId`
- `taskSlug`
- `targetUrl`
- `goal`
- `channel`
- `targetKeywords`
- `targetUrlPatterns`
- `targetFunctionNames`
- `targetActionDescription`
- `entry`

### `remove_breakpoint`

**Description:** 根据 ID 移除断点。可用 `list_breakpoints` 查看当前生效的断点。

**Parameters:**

- `breakpointId`

### `remove_hook`

**Description:** 按 id 移除一个 hook。

**Parameters:**

- `hookId`

### `remove_xhr_breakpoint`

**Description:** 移除一个 XHR/Fetch 断点。

**Parameters:**

- `url`

### `resume`

**Description:** 在断点暂停后继续执行 JavaScript，一直运行到下一个断点或执行结束。

### `risk_panel`

**Description:** 综合分析器、加密检测器与 hook 信号构建统一的风险评分。

**Parameters:**

- `code`
- `useAI`
- `includeHookSignals`
- `hookId`
- `topN`

### `search_in_scripts`

**Description:** 使用正则模式在已采集的脚本缓存中搜索。

**Parameters:**

- `pattern`
- `limit`
- `maxTotalSize`

### `search_in_sources`

**Description:** 在所有已加载的 JavaScript 源码中搜索字符串或正则表达式，返回匹配行及其 scriptId、URL 和行号。可用 `get_script_source` 配合 startLine/endLine 查看匹配处的完整上下文。

**Parameters:**

- `query`
- `caseSensitive`
- `isRegex`
- `maxResults`
- `maxLineLength`
- `excludeMinified`
- `urlFilter`

### `set_breakpoint`

**Description:** 在 JavaScript 文件的指定行设置断点，代码执行到该处时会触发。注意：监控函数调用时优先使用 `hook_function` 或 `create_hook`——断点需要配合暂停/继续执行，在自动化流程中容易出错。仅在需要查看函数内部局部变量时才使用断点。

**Parameters:**

- `url`
- `lineNumber`
- `columnNumber`
- `condition`
- `isRegex`

### `set_breakpoint_on_text`

**Description:** 通过搜索指定代码（函数名、语句等）并自动定位其精确位置来设置断点，普通文件和压缩文件都适用。注意：监控函数调用时优先使用 `hook_function`——它能在不暂停执行的情况下捕获参数/返回值。仅在需要查看某个具体代码位置的局部变量时才使用本工具。

**Parameters:**

- `text`
- `urlFilter`
- `occurrence`
- `condition`

### `set_user_agent`

**Description:** 为当前活动页面设置自定义 user-agent。

**Parameters:**

- `userAgent`

### `snapshot_scope`

**Description:** 在 JavaScript 执行暂停时，捕获一份有数量限制且已脱敏的作用域快照。命中断点后用它记录 local/closure/this/global 变量的名称、类型和安全预览值，用于动态数据流分析。

**Parameters:**

- `maxFrames`
- `maxVariablesPerScope`
- `maxStringLength`
- `includeGlobal`
- `taskId`
- `taskSlug`
- `targetUrl`
- `goal`

### `step_into`

**Description:** 步入下一个函数调用，用于进入并调试函数体。

### `step_out`

**Description:** 步出当前函数，一直执行到该函数返回。用它快速退出一个函数。

### `step_over`

**Description:** 单步跳过到下一条语句，把函数调用当作一步执行。用它在不进入函数体的情况下逐行执行代码。

### `stop_monitor`

**Description:** 停止一个事件监控。

**Parameters:**

- `monitorId`

### `summarize_code`

**Description:** 对单个代码文件、多个文件或项目级上下文进行摘要。

**Parameters:**

- `mode`
- `code`
- `url`
- `files`

### `trace_function`

**Description:** 根据源代码中的函数名追踪对该函数的调用。适用于任意函数，包括模块内部函数（webpack/rollup 打包的）。使用"日志点"（条件断点）在不暂停执行的情况下记录参数。

**Parameters:**

- `functionName`
- `urlFilter`
- `logArgs`
- `logThis`
- `pause`
- `traceId`
- `taskId`
- `taskSlug`
- `targetUrl`
- `goal`

### `understand_code`

**Description:** 结合 AI 与静态分析解析代码的结构、业务逻辑与安全性。

**Parameters:**

- `code`
- `focus`
- `useAI`

### `unhook_function`

**Description:** 移除先前安装的函数 hook。

**Parameters:**

- `hookId`
