# 输出契约

必须包含：

- 目标接口与字段
- 函数路径
- 运行时证据（hook 记录 + request 关联）
- 输入输出样例
- 补丁与回滚步骤
- 置信度与不确定性
- task artifact 路径
- targetContext（至少包含 `targetActionDescription` 或其他目标边界）
- 本地补环境状态（已补/未补）
- 动态分析启用状态；如果启用高级报告，必须说明 `advancedDynamicAnalysis` 的关键证据与边界
