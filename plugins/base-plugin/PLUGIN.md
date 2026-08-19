# base-plugin — AI 编程底座

> 本文件由 SessionStart hook 注入会话上下文（对标 ECS ecs-qa 的 CLAUDE.md）。

## 可用命令

| 命令 | 用途 |
|------|------|
| `/base-plugin:project-init` | 在**业务 git 项目**初始化 CLAUDE.md、`.claude/rules` |
| `/base-plugin:code-review` | 对 git diff 做分级审查 |

## 推荐流程（与 OpenSpec 联用）

1. 新项目：`/base-plugin:project-init`
2. 需求：`/opsx:propose "..."` → 审阅 proposal / tasks
3. 实现：`/opsx:apply`
4. 审查：`/base-plugin:code-review`
5. 归档：`/opsx:archive`

## 注意

- 不要在 `my-ai-platform` 插件仓库里跑 project-init
- code-review 默认只输出报告，不直接改代码，除非用户明确要求
