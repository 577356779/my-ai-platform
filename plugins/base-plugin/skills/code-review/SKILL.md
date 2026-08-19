---
name: code-review
description: |
  对当前 git diff 或指定文件做代码审查，输出分级问题清单和修改建议。
  触发词：code review、代码审查、帮我 review、审查改动、review diff
---

# Code Review

## 何时使用

用户要求审查代码、review PR、或检查最近改动时使用。

## 流程

1. 若无指定文件，执行 `git diff` 和 `git diff --staged` 获取改动
2. 按以下维度检查：正确性、边界条件、安全、可读性、测试覆盖
3. 输出 Markdown 表格：严重程度 | 位置 | 问题 | 建议

## 输出格式

- 🔴 必须改：逻辑错误、安全风险、明显 bug
- 🟡 建议改：可读性、命名、缺少测试
- 🟢 可选优化：风格、微优化

默认只输出审查报告，不直接改代码，除非用户明确要求修复。
