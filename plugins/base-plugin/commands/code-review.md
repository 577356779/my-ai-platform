---
description: 审查当前代码改动
argument-hint: [可选：文件路径或 PR 描述]
---

对当前仓库的代码改动做审查。

**步骤：**

1. 若无 $ARGUMENTS，先执行 `git diff` 和 `git diff --staged` 获取改动
2. 加载并遵循 `base-plugin:code-review` skill 的流程
3. 输出分级审查报告；默认不直接改代码，除非用户明确要求
