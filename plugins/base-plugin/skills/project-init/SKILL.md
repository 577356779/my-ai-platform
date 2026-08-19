---
name: project-init
description: |
  为当前 git 项目初始化 AI 协作骨架：CLAUDE.md、.claude/ 目录、基础 rules。
  触发词：project init、初始化项目、项目接入、生成 CLAUDE.md、init project
---

# Project Init

## 何时使用

用户要在**当前 git 仓库**里接入 AI 编程协作结构时使用（不是 marketplace 插件仓库本身）。

## 目标产物

在项目根目录生成或补全：

```text
项目根/
├── CLAUDE.md                 # 项目级 AI 上下文（核心）
└── .claude/
    └── rules/
        └── coding.md         # 基础编码约束（若不存在）
```

可选（用户同意且 `openspec` 在 PATH 上时）：

```text
openspec/
└── config.yaml               # 由 openspec init 创建
```

## 流程

### 1. 前置检查

- 确认当前目录是 git 仓库（存在 `.git`）；否则提示先 `git init` 或 `cd` 到正确项目
- 若已在 marketplace 插件仓库内（存在 `.claude-plugin/marketplace.json`），提醒用户应在**业务项目**里执行，不要改插件市场仓库

### 2. 收集信息（缺失则 AskUserQuestion）

- 项目名称 / 一句话描述
- 技术栈（如 Java + Spring、Vue 3、Python 等）
- 主要目录说明（如 `src/`、`apps/`）
- 测试命令（如 `npm test`、`mvn test`）
- 特殊约束（内网依赖、禁止改哪些目录等）

### 3. 写 CLAUDE.md

- **已存在且非空**：先展示当前内容摘要，AskUserQuestion 选择「合并补充」或「跳过」
- **不存在或用户同意覆盖**：写入下列模板并填入收集到的信息

模板结构：

```markdown
# <项目名>

<一句话描述>

## 技术栈

- ...

## 目录结构

- ...

## 常用命令

| 命令 | 用途 |
|------|------|
| ... | ... |

## 编码约束

- ...

## AI 工作流

- 需求/功能：优先 OpenSpec（`/opsx:propose` → `/opsx:apply` → `/opsx:archive`）
- 代码审查：`/base-plugin:code-review`
```

### 4. 写 .claude/rules/coding.md

仅当文件不存在时创建，内容保持简短：

- 改前先读相关代码，做最小 diff
- 不提交 secret / .env
- 改完运行项目约定的测试命令（写入具体命令）

### 5. OpenSpec（可选）

- 运行 `which openspec` 或 `openspec --version`
- 已安装且尚无 `openspec/`：问用户是否执行 `openspec init`；同意则运行并简要说明 `/opsx:*` 命令
- 未安装：只提示 `npm i -g @fission-ai/openspec`，不阻断

### 6. 收尾

输出已创建/更新的文件清单，并给出下一步建议：

```text
/opsx:propose "你的第一个需求"
/base-plugin:code-review
```

## 注意事项

- 不修改 `.gitignore` 除非用户明确要求
- 不安装 npm 全局包（除用户明确同意执行 openspec init 且 CLI 已存在）
- 一次 init 只处理一个项目根目录
