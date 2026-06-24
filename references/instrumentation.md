# 插桩策略（含 VMP）

- Hook-first，Breakpoint-last。
- 动态分析优先使用 `collect_code` 的 runtime sampler 和 advanced report；只有需要暂停现场时再进入断点。
- VMP 首轮最小采样：opcode、ip/pc、栈顶摘要、关键寄存器摘要、输出摘要。
- 禁止首轮全量状态采样。
- 敏感明文证据必须显式 opt-in，默认只记录 key、长度、调用栈和依赖 hint。

## 配套工具

- 定位加密/签名发生处：`hook_crypto_apis` 一键 hook 编码/加密/(可选)定时 API 组（JSON、atob/btoa、encode·decodeURI、escape、WebCrypto…），记录参数 + 调用栈。
- 目标有 `debugger` 反调试时：`neutralize_debugger` 外科手术式中和（只跳 debugger、保留你自己的断点），与断点/插桩共存。
- WASM 承载逻辑：`disassemble_wasm` 把 wasm 反汇编成 wat 文本阅读（配合 `inspect_wasm` 看导出/导入）。

## jsvmp 插桩 trace 工具链

定位 + 插桩 + 重建已工具化，按序使用：

1. `get_script_source` 取目标脚本源码。
2. `instrument_jsvmp`：自动定位 dispatcher（巨型 switch + 无限循环 + 索引判别式），给分发 switch 注入探针（判别式只求值一次、语义保持），产出插桩源码 + 探针引导片段。
3. 用请求拦截 / `setScriptSource` 把页面脚本换成插桩源码；探针缓冲区用 `inject_preload_script` 装好（或 `instrument_jsvmp` 的 `registerProbe`）。
4. 触发目标行为后，`evaluate_script` 读回 `window.<probe>Buffer`。
5. `reconstruct_jsvmp_trace`：把 opcode 流重建成指令流（热点直方图 + 执行序列 + opcode→handler 源码映射）。

探针 / 缓冲的 toString、stack 由 `inject_stealth` 的 `nativeToStringSpoof`、`errorStackClean` 隐身，避免目标自检发现插桩。
