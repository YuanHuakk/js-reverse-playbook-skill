# 补环境规范

- 先读代理 env log，再确认 `first divergence`，最后决定补丁。
- 一次只做一个补丁决策；该决策应对应一个最小因果单元。
- 最小因果单元可以是：值 / 函数壳 / 返回对象 / 最小对象契约。
- `diff_env_requirements` 仅作辅助，不替代代理日志。
- 每次补丁都要可回滚、可复测、可追溯到页面证据。
- 常见项：`navigator`、`webdriver`、`crypto`、`atob/btoa`、`TextEncoder`。
- 避免一次性全局模拟浏览器。
- 没有代理日志或没有 `first divergence` 记录时，不允许直接补宿主。

## 基线与自动闭环

- `export_rebuild_bundle` 的 `envBaseline`：默认 `minimal`（零依赖手搓，强检测目标首选）；`jsdom`（DOM 基线 + core-js + L2 保真覆盖层，DOM 密集目标提速）。
- 切 `jsdom` 后，指纹敏感项仍以 L2 覆盖层的页面证据为准，不要信 jsdom 默认值。
- `auto_patch_env`：闭环自动补——跑→读 first divergence→套注册表补丁→重跑，默认 ≤6 轮。
- 自动闭环只补低风险宿主缺口；遇 `fetch`/`XHR`/自定义检测等注册表外缺口会停下交回人工。
