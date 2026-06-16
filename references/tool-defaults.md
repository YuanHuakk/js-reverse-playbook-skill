# 工具参数默认值

- search_in_scripts: sign|token|nonce|encrypt|hmac|sha|md5|cookie|h5st
- get_hook_data: view=summary, maxRecords=80
- 首轮 Hook: fetch + xhr
- record_reverse_evidence: channel=runtime-evidence
- export_rebuild_bundle: 优先导出 env/entry.js + env/env.js + env/polyfills.js + env/capture.json
- 断点默认禁用，只有兜底时启用
- collect_code: 默认不开启高侵入动态分析；需要参数链路关联时再传 `enableRuntimeSampler=true`
- collect_code: 需要高级动态报告时再传 `enableAdvancedDynamicAnalysis=true` 和 `targetParams`
- collect_code: `includeSensitiveEvidence=false`；只有授权任务才允许显式改为 `true`
