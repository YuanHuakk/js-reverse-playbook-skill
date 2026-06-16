# 插桩策略（含 VMP）

- Hook-first，Breakpoint-last。
- 动态分析优先使用 `collect_code` 的 runtime sampler 和 advanced report；只有需要暂停现场时再进入断点。
- VMP 首轮最小采样：opcode、ip/pc、栈顶摘要、关键寄存器摘要、输出摘要。
- 禁止首轮全量状态采样。
- 敏感明文证据必须显式 opt-in，默认只记录 key、长度、调用栈和依赖 hint。
