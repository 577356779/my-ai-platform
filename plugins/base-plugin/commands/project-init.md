---
description: 为当前 git 项目初始化 CLAUDE.md 与 .claude/rules 协作骨架
argument-hint: [可选：项目一句话描述或技术栈]
---

为**当前目录所在的 git 项目**初始化 AI 协作结构（不是 my-ai-platform 插件仓库）。

**步骤：**

1. 确认当前路径是业务项目的 git 根目录（有 `.git`）；若是插件 marketplace 仓库则提示用户切换到目标项目
2. 加载并遵循 `base-plugin:project-init` skill 的完整流程
3. 若提供 $ARGUMENTS，将其作为项目描述/技术栈的补充信息
4. 完成后列出新建或更新的文件，并提示 `/opsx:propose` 与 `/base-plugin:code-review`
