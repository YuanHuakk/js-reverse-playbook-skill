# Node 扣包补环境复现

- 扣最小函数依赖，不整包搬运。
- 每轮只补 1 个缺失点并重跑。
- 超过 6 个补丁未收敛则停止，回浏览器取证。
- 机械性宿主缺口可交 `auto_patch_env` 闭环自动补（默认 ≤6 轮，同样的收敛上限）。
- DOM 操作密集且非强检测的目标，可用 `export_rebuild_bundle` 的 `envBaseline=jsdom` 铺底。
