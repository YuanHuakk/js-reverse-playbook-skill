# AST 去混淆流程

`deobfuscate_code` 按检测结果先做专项脱壳，再走通用 pass。

专项脱壳（自动触发，node `vm` 沙箱实际求值）：

- jsfuck 解码（`[]()!+` 表达式不动点求值）
- sojson 字符串解密（注入「数组 + 旋转 + 解密函数」三件套还原调用）
- obfuscator.io 字符串解密（动态定位解密器 + ReferenceError 二次解密闭环）

沙箱重建失败兜底：当解密器与页面状态深度耦合、提取不出三件套时，用 `decrypt_calls_in_page`
在页面里用目标**活的**解密函数还原 `decryptName(...)` 调用（前置：页面已执行过目标脚本、
解密函数全局可达；babel 在 node 侧，只把调用经 evaluate 丢页面求值）。

通用 pass 固定顺序：

1. 常量传播
2. 字符串表替换
3. 控制流还原
4. 死代码清理
