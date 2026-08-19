# my-platform → ECS 对标教学方案（详细版）

> **文档版本**：2.0 · **维护路径**：`docs/ECS-ROADMAP.md`  
> **用法**：每天只做「当前 Day」；完成后勾选 checkbox → 更新 [进度快照](#进度快照) → `git commit` → 进入下一 Day。  
> **原则**：做为主、读为辅；**每项必须在真实 git 项目里验证**（插件仓库 `my-ai-platform` 不算业务项目）。

---

## 目录

1. [进度快照](#进度快照)
2. [术语与 ECS 映射](#术语与-ecs-映射)
3. [环境与工具清单](#环境与工具清单)
4. [每日固定节奏](#每日固定节奏)
5. [阶段 0：最小闭环](#阶段-0最小闭环)
6. [阶段 1：底座补全（Day 8–14）](#阶段-1底座补全day-814)
7. [阶段 2：工具包 my-tools（Day 15–19）](#阶段-2工具包-my-toolsday-1519)
8. [阶段 3：项目集成（Day 20–23）](#阶段-3项目集成day-2023)
9. [阶段 4：薄 CLI（Day 24–28）](#阶段-4薄-cliday-2428)
10. [阶段 5：第二职能包（Day 29–34）](#阶段-5第二职能包day-2934)
11. [阶段 6：联邦与分发（Day 35–38）](#阶段-6联邦与分发day-3538)
12. [阶段 7：文档站与 CI（Day 39–43）](#阶段-7文档站与-ciday-3943)
13. [阶段 8：进阶可选](#阶段-8进阶可选)
14. [版本发版手册](#版本发版手册)
15. [附录 A：Day 8 完整文件模板](#附录-a-day-8-完整文件模板)
16. [附录 B：验收命令速查](#附录-b验收命令速查)

---

## 进度快照

> **每次完成任务后更新下表**（复制到 commit message 也行）。

| 字段 | 值 |
|------|-----|
| **当前阶段** | 阶段 1 |
| **当前 Day** | **Day 9** |
| **当前进行项** | reviewer agent + 更新 code-review 命令 |
| **base-plugin 版本** | 0.1.2 |
| **my-tools 版本** | — |
| **已完成 Day** | 0.1–0.5、8（阶段 0 差 0.6） |
| **最后更新** | 2026-08-19 |
| **阻塞** | GitHub DNS/443 不可用 → marketplace 用本地路径 |

### 阶段总览

| 阶段 | Day 范围 | 对标 ECS | 状态 |
|------|---------|---------|------|
| 0 | 0.1–0.6 | 能装能用 + 狗食 | 🔄 差 0.6 |
| 1 | 8–14 | `core/` hooks·rules·agents·registry | 🔄 Day 8 完成，Day 9 进行中 |
| 2 | 15–19 | `packages/tools` | ⬜ |
| 3 | 20–23 | `.ecs/project.json` + doctor | ⬜ |
| 4 | 24–28 | `ecs-cli` 子集 | ⬜ |
| 5 | 29–34 | `packages/wiki` 或 `qa` 迷你 | ⬜ |
| 6 | 35–38 | `teams/teams.json` | ⬜ |
| 7 | 39–43 | `docs/` + CI | ⬜ |
| 8 | 按需 | 遥测·scaffold·harness | ⬜ |

### Day 勾选总表（快速浏览）

| Day | 标题 | 状态 |
|-----|------|------|
| 0.6 | dogfood-notes | [ ] |
| 8 | SessionStart hook | [x] |
| 9 | reviewer agent | [ ] |
| 10 | rules | [ ] |
| 11 | ecs-registry | [ ] |
| 12 | 狗食复盘改 skill | [ ] |
| 13 | architecture.md | [ ] |
| 14 | 缓冲 / push | [ ] |
| 15–43 | 见各阶段章节 | [ ] |

---

## 术语与 ECS 映射

| 你的名词 | ECS 名词 | 说明 |
|---------|---------|------|
| `my-platform` | ECS monorepo + marketplace | 插件市场仓库 |
| `base-plugin` | `core` 插件 `ecs` | 底座，人人必装 |
| `my-tools` | `packages/tools` → `ecs-tools` | 通用工具插件 |
| `PLUGIN.md` | `packages/qa/CLAUDE.md` | 插件级编排说明，供 hook 注入 |
| `.myplatform/` | `.ecs/` | 项目级集成目录（阶段 3 引入） |
| `catalog/registry.json` | `core/catalog/registry.json` | skill 中央索引 |
| OpenSpec `/opsx:*` | ECS 底座 OpenSpec | **不嵌入插件**，PATH 安装 |
| 本地 marketplace 路径 | 无直接等价 | 开发期替代 GitHub |

---

## 环境与工具清单

### 必需（阶段 0 起）

| 工具 | 检查命令 | 用途 |
|------|---------|------|
| Claude Code | `claude --version` | 运行插件 |
| git | `git --version` | 业务项目 |
| Node.js ≥18 | `node --version` | 将来 CLI |
| OpenSpec | `openspec --version` | `/opsx:*` 流程 |

### 安装 OpenSpec（若未装）

```bash
npm i -g @fission-ai/openspec
```

### 插件安装（开发期固定用法）

```bash
# Claude Code 会话内
/plugin marketplace add /Users/hsren2/iflytek/myworkspace/my-ai-platform
/plugin install base-plugin@my-platform
/plugin update base-plugin@my-platform   # 改插件后
```

### 两个仓库的区分（重要）

| 仓库 | 路径示例 | 用途 |
|------|---------|------|
| **插件市场** | `my-ai-platform/` | 写 skill、hook、marketplace |
| **业务项目** | 任意有 `.git` 的项目 | project-init、opsx、review |

**禁止**在 `my-ai-platform` 里跑 `/base-plugin:project-init`（除非故意测试）。

---

## 每日固定节奏

| 时段 | 做什么 | 时长 |
|------|--------|------|
| 晨 | 打开本文 → 读当日「ECS 对照阅读」 | 20–30 min |
| 主 | 按步骤改文件 → bump 版本 → update 插件 | 60–90 min |
| 晚 | **业务项目**跑验收命令 → 勾选 checkbox → 更新进度快照 → commit | 20–30 min |

**Commit 前缀**：`feat:` 新能力 · `fix:` 修 bug · `docs:` 纯文档 · `chore:` 版本/配置

---

# 阶段 0：最小闭环

> **目标**：证明「marketplace → 插件 → OpenSpec → review」在真实项目可行。

## Day 0.1–0.5 ✅（你已完成，留作对照）

<details>
<summary>展开查看已完成项验收标准</summary>

| 任务 | 验收 |
|------|------|
| marketplace + base-plugin | `/plugin install base-plugin@my-platform` 成功 |
| code-review | `/base-plugin:code-review` 有分级报告 |
| project-init | 业务项目出现 `CLAUDE.md` |
| opsx 全流程 | `openspec/changes/archive/` 有归档目录 |
| review ×2 | 同一 change 中至少 2 次 review |

</details>

---

## Day 0.6 — 狗食笔记（今天可先做这个，30 分钟）

### 学习目标

- 把「用起来」变成「可改进的文档」，供 Day 12 改 skill 用。

### 操作步骤

1. 打开 `docs/dogfood-notes.md`
2. 在「2026-08-19」一节补全（至少 5 条观察），例如：
   - propose 哪步最费 token
   - review 是否检查了测试
   - project-init 生成的 CLAUDE.md 缺什么
   - 是否忘记 slash 前缀
   - OpenSpec 与 review 顺序是否合理
3. 勾选 `[x] Day 0.6`
4. 更新进度快照：当前 Day → **8**

### 验收

- [ ] `docs/dogfood-notes.md` ≥5 条具体观察（非空话）

### Commit

```bash
git add docs/dogfood-notes.md docs/ECS-ROADMAP.md
git commit -m "docs: complete dogfood notes for phase 0"
```

---

# 阶段 1：底座补全（Day 8–14）

> **对标 ECS**：`core/` 的 hooks、rules、agents、skill 注册表。  
> **阶段结束标志**：base-plugin ≥ **0.1.5**，SessionStart 注入生效。

---

## Day 8 — SessionStart hook（核心日，2–3 小时）

### 学习目标

- 理解 ECS `ecs-qa` 如何在会话开始时注入 `<ecs-qa-context>`
- 实现 `base-plugin` 的 `<base-plugin-context>` 自动简报

### ECS 对照阅读（按顺序，约 25 分钟）

| 顺序 | 文件 | 重点看什么 |
|------|------|-----------|
| 1 | `ECS/packages/qa/hooks/session-start` | JSON 输出格式、`escape_for_json`、`exit 0` |
| 2 | `ECS/packages/qa/hooks/hooks.json` | SessionStart matcher、`CLAUDE_PLUGIN_ROOT` |
| 3 | `ECS/packages/qa/CLAUDE.md` 前 40 行 | 被注入的编排内容长什么样 |
| 4 | `ECS/docs/architecture/hook-system-overview.md` | 包级 hook vs 系统级 hook |

### 原理（1 分钟）

```text
SessionStart 触发
    → 运行 hooks/session-start.sh
    → 读取 PLUGIN.md
    → 输出 additionalContext JSON
    → Claude 会话上下文最前面出现 <base-plugin-context>
    → 模型「知道」有哪些命令、推荐什么流程
```

### 操作步骤

#### Step 1：创建 `PLUGIN.md`

路径：`plugins/base-plugin/PLUGIN.md`

内容见 [附录 A.1](#a1-pluginmd)（可直接复制）。

#### Step 2：创建 `hooks/session-start.sh`

路径：`plugins/base-plugin/hooks/session-start.sh`

内容见 [附录 A.2](#a2-session-startsh)。

```bash
chmod +x plugins/base-plugin/hooks/session-start.sh
```

#### Step 3：替换 `hooks/hooks.json`

**删除**模板里的 `PreToolUse` / `example-guard.sh`（会干扰 Bash，与 ECS 底座无关）。

完整内容见 [附录 A.3](#a3-hooksjson)。

#### Step 4：版本 bump

| 文件 | 改什么 |
|------|--------|
| `plugins/base-plugin/.claude-plugin/plugin.json` | `"version": "0.1.2"` |
| `.claude-plugin/marketplace.json` | plugins[0].version → `0.1.2` |
| `.release-please-manifest.json` | `"plugins/base-plugin": "0.1.2"` |

#### Step 5：更新插件

```bash
# Claude Code 内
/plugin update base-plugin@my-platform
# 若不生效：
/plugin uninstall base-plugin@my-platform
/plugin install base-plugin@my-platform
```

#### Step 6：验证（必须新开会话）

```text
# 新 Claude Code 会话，在业务项目目录
我装了哪些 base-plugin 能力？推荐什么开发流程？
```

期望：回答中出现 `project-init`、`code-review`、`/opsx:propose` 等关键词。

#### Step 7：勾选与 commit

```bash
git add plugins/base-plugin/ .claude-plugin/ .release-please-manifest.json
git commit -m "feat(base-plugin): SessionStart hook injects PLUGIN.md context"
```

### 验收清单

- [ ] `session-start.sh` 可执行（`chmod +x`）
- [ ] `hooks.json` 仅有 SessionStart，无 PreToolUse
- [ ] 新会话 Claude 能复述推荐流程
- [ ] hook 手动运行 exit 0：

```bash
cd plugins/base-plugin
CLAUDE_PLUGIN_ROOT="$(pwd)" bash hooks/session-start.sh | head -c 200
# 应看到 JSON 和 base-plugin-context
```

### 常见问题

| 现象 | 处理 |
|------|------|
| 新会话无上下文 | 是否新开会话；是否 update 插件；查 `~/.claude/plugins/cache/` 下版本是否 0.1.2 |
| JSON 解析失败 | `PLUGIN.md` 含大量 `"` 或反斜杠 → 检查 escape 函数 |
| hook 报错阻断会话 | 脚本末尾必须 `exit 0` |

---

## Day 9 — reviewer agent（1.5 小时）

### 学习目标

- 对标 ECS 的 agent 壳：隔离审查上下文，command 派 agent 而非直接聊

### ECS 对照阅读

- `ECS/docs/reference/core-capabilities.md` — agent 列表
- `ECS/docs/guide/agent-development.md` 前 60 行
- 任选一个 `ECS/core/agents/*.md` 看 frontmatter

### 操作步骤

#### Step 1：创建 `agents/reviewer.md`

```markdown
---
name: reviewer
description: 代码审查专家。只输出审查报告，不修改代码，除非用户明确要求。
model: sonnet
---

你是严格的 code reviewer。

## 职责

- 阅读 git diff 或指定文件
- 按 code-review skill 的维度输出分级表
- 不直接改代码

## 输出

使用 🔴/🟡/🟢 分级，表格列：严重程度 | 位置 | 问题 | 建议
```

#### Step 2：修改 `commands/code-review.md`

在步骤中增加：

```markdown
2. 使用 **reviewer** agent 执行审查（或 Skill 工具加载 `base-plugin:code-review`）
3. 输出分级审查报告
```

#### Step 3：bump → 0.1.3，update 插件，验证

```text
/base-plugin:code-review
```

### 验收

- [ ] `agents/reviewer.md` 存在且 frontmatter 完整
- [ ] review 输出仍正常

---

## Day 10 — rules（1 小时）

### 学习目标

- 对标 `ECS/core/rules/`：常驻编码约束

### ECS 对照阅读

- `ECS/docs/guide/rule-development.md`
- `ECS/core/rules/` 任选一文件

### 操作步骤

#### Step 1：创建 `rules/coding-discipline.md`

```markdown
---
description: 基础编码纪律，所有代码改动必须遵守
---

# Coding Discipline

1. **先读后改**：修改前阅读相关文件与调用方
2. **最小 diff**：只改任务相关文件，不顺手重构
3. **禁止 secret**：不提交 .env、token、密码
4. **验证**：改完运行项目测试命令（见 CLAUDE.md）
5. **审查门禁**：合并前建议 `/base-plugin:code-review`
```

> Claude Code 插件的 `rules/` 目录会随插件加载（与 ECS 包级 rules 类似）。

#### Step 2：在 `PLUGIN.md` 增加一节「Rules」

```markdown
## Rules

插件自带 `rules/coding-discipline.md`，会话内常驻。
```

#### Step 3：bump → 0.1.4，验证

新会话问：「改代码要遵守什么规则？」应能提到 discipline 要点。

### 验收

- [ ] `rules/coding-discipline.md` 存在
- [ ] PLUGIN.md 提到 rules

---

## Day 11 — ecs-registry.json（1.5 小时）

### 学习目标

- 对标 ECS 联邦「能力注册文件」→ 中央 `registry.json`

### ECS 对照阅读

- `ECS/docs/architecture/federation.md` 全文
- `ECS/packages/tools/ecs-registry.json`

### 操作步骤

#### Step 1：`plugins/base-plugin/ecs-registry.json`

```json
{
  "package": "base-plugin",
  "team": "hsren",
  "category": "foundation",
  "skills": [
    {
      "id": "code-review",
      "name": "code-review",
      "description": "对 git diff 做分级代码审查",
      "version": "0.1.0",
      "stages": ["review"]
    },
    {
      "id": "project-init",
      "name": "project-init",
      "description": "初始化 CLAUDE.md 与 .claude/rules",
      "version": "0.1.0",
      "stages": ["setup"]
    },
    {
      "id": "skill-creator",
      "name": "skill-creator",
      "description": "创建与优化 skill（Anthropic 模板）",
      "version": "0.1.0",
      "stages": ["meta"]
    }
  ]
}
```

#### Step 2：`catalog/registry.json`（根目录）

```json
{
  "generatedAt": "2026-08-19",
  "skills": [
    {
      "id": "code-review",
      "package": "base-plugin",
      "team": "hsren",
      "category": "foundation",
      "description": "对 git diff 做分级代码审查",
      "version": "0.1.0",
      "stages": ["review"]
    },
    {
      "id": "project-init",
      "package": "base-plugin",
      "team": "hsren",
      "category": "foundation",
      "description": "初始化 CLAUDE.md 与 .claude/rules",
      "version": "0.1.0",
      "stages": ["setup"]
    },
    {
      "id": "skill-creator",
      "package": "base-plugin",
      "team": "hsren",
      "category": "foundation",
      "description": "创建与优化 skill",
      "version": "0.1.0",
      "stages": ["meta"]
    }
  ]
}
```

#### Step 3：`docs/skill-catalog.md`

人工可读表格，列：id | 插件 | 命令 | 一句话。

#### Step 4：commit（可不 bump 插件版本，纯注册表）

### 验收

- [ ] 三个 skill id 全局唯一
- [ ] `ecs-registry.json` 与 `catalog/registry.json` 一致

---

## Day 12 — 狗食复盘 + 改 1 处 skill（2 小时）

### 操作步骤

1. 重读 `docs/dogfood-notes.md`
2. 选 **一处**修改：
   - review 太泛 → `skills/code-review/SKILL.md` 加「必须列出测试是否运行」
   - init 问太多 → `project-init` 减问题、加默认值
3. 业务项目再跑：**1 次 opsx 小 change + 1 次 review**
4. bump → **0.1.5**
5. 在 dogfood-notes 追加「Day 12 改进效果」

### 验收

- [ ] skill 有实质改动（非改错别字）
- [ ] 真实项目跑通

---

## Day 13 — architecture.md（1 小时）

### 操作步骤

创建 `docs/architecture.md`，包含：

1. 你的四层图（marketplace / base-plugin / 将来 my-tools / teams）
2. 与 ECS 差异表（至少 8 行）
3. 目录结构树（当前 `my-ai-platform/`）
4. 数据流：用户安装 → SessionStart → command → skill

更新 README 链接。

### 验收

- [ ] 新人只看 architecture + README 能解释 base-plugin 边界

---

## Day 14 — 缓冲日

- [ ] 补勾选遗漏
- [ ] 尝试 `git push`（网络允许）
- [ ] 更新进度快照：阶段 1 → ✅，当前 Day → **15**

---

# 阶段 2：工具包 my-tools（Day 15–19）

> **原则**：**新插件独立目录**，不要继续往 base-plugin 堆 git 能力。

## Day 15 — 新建 my-tools 插件（2 小时）

### ECS 对照

- `ECS/packages/tools/.claude-plugin/plugin.json`
- `ECS/.claude-plugin/marketplace.json` 中 `ecs-tools` 条目

### 目录结构（目标）

```text
plugins/my-tools/
├── .claude-plugin/plugin.json
├── PLUGIN.md
├── commands/
├── skills/
└── README.md
```

### plugin.json 初始

```json
{
  "name": "my-tools",
  "version": "0.1.0",
  "description": "通用工具：Git 提交规范等",
  "skills": ["./skills/"],
  "commands": ["./commands/"]
}
```

### marketplace.json 追加

```json
{
  "name": "my-tools",
  "description": "通用 Git / 文档工具",
  "source": "./plugins/my-tools",
  "version": "0.1.0",
  "keywords": ["git", "commit", "tools"]
}
```

### 验收

```bash
/plugin install my-tools@my-platform
/plugin   # 应看到 base-plugin + my-tools
```

---

## Day 16 — git-commit（2 小时）

### ECS 对照

- `ECS/packages/tools/skills/git-workflow/SKILL.md` — commit message 章节
- `ECS/packages/tools/skills/git-workflow/references/git-commit-message.md`（若有）

### skill 要点

- 读 `git diff --staged`
- 输出 `type(scope): subject` + body 要点
- type：feat/fix/docs/chore/refactor/test
- **不自动 commit**，除非用户确认

### command

`/my-tools:git-commit`

### 验收

- [ ] staged 有改动时生成合理 message 草稿
- [ ] 业务项目用过 1 次

---

## Day 17 — spec-branch 文档（1 小时）

### ECS 对照

- `ECS/packages/tools/skills/spec-git-flow/SKILL.md`（只读，不整包搬）

### 交付

- `docs/workflows/spec-git.md`：feature 分支命名 `feature/<change-id>` 与 `openspec/changes/<change-id>/` 对应
- 可选轻量 skill `spec-branch`（仅文档型，无脚本）

---

## Day 18 — my-tools PLUGIN + 合并 SessionStart 提示（1 小时）

- `plugins/my-tools/PLUGIN.md` 只列 tools 命令
- 更新 `base-plugin/hooks/session-start.sh`：在 context 末尾加一行「已安装 my-tools 时可用 /my-tools:git-commit」（或读环境变量/simple 检测）

---

## Day 19 — 阶段 2 狗食（2 小时）

**一条完整流水线**（业务项目）：

```text
/base-plugin:project-init          # 若已 init 可跳过
/opsx:propose "小功能"
/opsx:apply
/base-plugin:code-review
/my-tools:git-commit
/opsx:archive
```

### 阶段 2 完成标准

- [ ] marketplace 2 插件
- [ ] 上述流水线手动跑通 1 次
- [ ] 进度快照：阶段 2 ✅，Day → **20**

---

# 阶段 3：项目集成（Day 20–23）

> 对标 `ecs-cli project init` + `.ecs/project.json` + `doctor`

## Day 20 — `.myplatform/project.json`

### ECS 对照

- `ECS/docs/reference/filesystem-layout.md`
- 业务项目 `.ecs/project.json` 示例（ECS 仓库 examples/）

### schema v1

```json
{
  "version": 1,
  "platform": "my-platform",
  "plugins": ["base-plugin", "my-tools"],
  "openspec": true,
  "initializedAt": "2026-08-19T12:00:00+08:00",
  "notes": "由 /base-plugin:project-init 生成"
}
```

### 修改 `project-init` skill

- init 结束时写入 `.myplatform/project.json`
- 若目录已存在则 merge plugins 列表，不覆盖 notes

### 验收

- [ ] 新业务项目 init 后出现该文件

---

## Day 21 — doctor command

### ECS 对照

- `ECS/docs/reference/cli-reference.md` — `ecs-cli doctor` 一节

### `commands/doctor.md` 检查项

| 检查 | 失败提示 |
|------|---------|
| 当前目录是 git 仓库 | 请 cd 到项目根 |
| `.myplatform/project.json` 存在 | 请运行 project-init |
| `openspec` 在 PATH | 可选警告，给安装命令 |
| `CLAUDE.md` 存在 | 建议 project-init |

输出 ✅/❌ 列表，**exit 语义由 Claude 呈现即可**（hook 不用于 doctor）。

### 验收

```text
/base-plugin:doctor
```

---

## Day 22–23 — 文档 + 双项目验证

- 更新 `docs/architecture.md` 增加「项目级 `.myplatform/`」
- 在 **第二个** git 项目跑 init + doctor + 一次 opsx

### 阶段 3 完成标准

- [ ] 两项目均有 `.myplatform/project.json`
- [ ] doctor 输出正确

---

# 阶段 4：薄 CLI（Day 24–28）

> **触发条件**：阶段 3 稳定；或团队 ≥3 人。

## Day 24–25 — CLI 骨架

### ECS 对照

- `ECS/cli/` 目录（只读结构，不必读 Go 重写部分）

### 目标结构

```text
cli/
├── package.json          # name: @hsren/my-cli, bin: my-cli
├── bin/my-cli.js         # 入口
├── lib/
│   ├── plugin.js         # install / list → spawn claude plugin ...
│   ├── project.js        # init → 写 .myplatform + 复制 CLAUDE 模板
│   └── doctor.js         # 检查逻辑
└── README.md
```

### 最小命令

```bash
my-cli plugin install base-plugin
my-cli project init
my-cli doctor
```

### 验收

```bash
node cli/bin/my-cli.js doctor
# 在业务项目根目录，输出与 slash doctor 等价的检查
```

---

## Day 26–28 — 文档与 npm link

- `npm link` 或 `npm i -g ./cli` 本地安装
- README「推荐安装路径」改为 CLI 优先
- **版本线分离**：CLI version 在 `cli/package.json`，与 base-plugin 独立

---

# 阶段 5：第二职能包（Day 29–34）

> **二选一**，写在进度快照里：`路径 A wiki` 或 `路径 B qa`

## 路径 A：my-wiki（推荐）

| Day | 交付 | ECS 对照 |
|-----|------|---------|
| 29 | `plugins/my-wiki` + marketplace 登记 | `packages/wiki/plugin.json` |
| 30 | `wiki-init` → `team-knowledge/wiki/` 骨架 | `wiki-init` skill |
| 31 | `wiki-ingest` → `contributions/` 草稿 | raw → contributions 流水线 |
| 32 | `wiki-query` 读 catalog 回答 | `wiki-query` skill |
| 33 | 狗食：入库 3 篇文档 | `docs/guide/wiki-workflow.md` |
| 34 | `docs/wiki-workflow.md` | — |

**不做**：EKnowledge Hub、hook 召回（ECS 0.4.x 以后才有）。

## 路径 B：my-qa 迷你版（测试团队）

| Day | 交付 |
|-----|------|
| 29–31 | `req-analysis` command + skill + `artifacts/req-digest.md` 约定 |
| 32–33 | `test-case` command + 简单用例表模板 |
| 34 | 文档 `docs/qa-mini-workflow.md` |

**禁止**：一次搬 ECS 21 个 skill。

---

# 阶段 6：联邦与分发（Day 35–38）

## Day 35 — teams.json

### ECS 对照

- `ECS/teams/teams.json`
- `ECS/docs/team/joining.md`

### 文件示例

```json
{
  "$schema": "https://anthropic.com/claude-code/ecs-teams.schema.json",
  "description": "my-platform 团队插件登记",
  "teams": [
    {
      "name": "example-team-skills",
      "type": "marketplace",
      "description": "示例：未来某团队的插件市场（占位）",
      "repo": {
        "https": "https://github.com/577356779/example-team-skills.git",
        "ref": "main"
      }
    }
  ]
}
```

文档说明：`ecs-cli team register` 在 ECS 里做什么；你阶段 6 可 **手动** `marketplace add` 第二 repo。

## Day 36–37 — 远程安装

- 配置 SSH 或 HTTPS 代理
- `git push`
- 同事远程安装并完成 init + review

## Day 38 — CHANGELOG + 发布说明

---

# 阶段 7：文档站与 CI（Day 39–43）

## Day 39–41 — VitePress

### ECS 对照

- `ECS/docs/.vitepress/config.ts`
- `ECS/docs/index.md`

### 最小页面

- `/` 首页
- `/guide/getting-started`
- `/reference/skill-catalog`
- `/architecture/overview`

```bash
cd docs-site && pnpm init && pnpm add -D vitepress
# 或复制 ECS docs 结构精简版
```

## Day 42–43 — CI

```yaml
# .github/workflows/validate.yml 伪代码
- run: node scripts/validate-marketplace.js
# 检查 marketplace name 与 plugin.json name 一致
# 检查 catalog skill id 唯一
```

可从 ECS `.github/scripts/validate-registry.mjs` 简化为 50 行内脚本。

---

# 阶段 8：进阶可选

| 能力 | ECS 位置 | 建议时机 |
|------|---------|---------|
| Hook 遥测 JSONL | `docs/guide/analytics.md` | 团队 >10 人 |
| scaffolds | `ECS/scaffolds/` | 有统一项目模板时 |
| workflow-studio | `ECS/apps/workflow-studio` | 不建议 2026 前做 |
| harness Cursor | `ECS/harness/` | base 稳定后 |
| npm 私服发布 CLI | ESkillHub | 企业有 Artifactory 时 |

---

# 版本发版手册

每次改 `plugins/*` 内行为（非纯 docs）：

```text
1. 改 plugins/<name>/.claude-plugin/plugin.json → version patch/minor
2. 改 .claude-plugin/marketplace.json → 对应条目 version
3. 改 .release-please-manifest.json
4. git commit -m "feat(base-plugin): ..."
5. Claude Code: /plugin update <name>@my-platform
6. 新会话验证
7. 更新本文进度快照里的版本号
```

---

# 附录 A：Day 8 完整文件模板

## A.1 PLUGIN.md

路径：`plugins/base-plugin/PLUGIN.md`

```markdown
# base-plugin — AI 编程底座

> 本文件由 SessionStart hook 注入会话上下文（对标 ECS ecs-qa 的 CLAUDE.md）。

## 可用命令

| 命令 | 用途 |
|------|------|
| `/base-plugin:project-init` | 在**业务 git 项目**初始化 CLAUDE.md、.claude/rules |
| `/base-plugin:code-review` | 对 git diff 做分级审查 |
| `/base-plugin:doctor` | 检查项目集成状态（Day 21 起） |

## 推荐流程（与 OpenSpec 联用）

1. 新项目：`/base-plugin:project-init`
2. 需求：`/opsx:propose "..."` → 审阅 proposal/tasks
3. 实现：`/opsx:apply`
4. 审查：`/base-plugin:code-review`
5. 提交：`/my-tools:git-commit`（安装 my-tools 后，Day 16 起）
6. 归档：`/opsx:archive`

## Rules

插件自带 `rules/coding-discipline.md`（Day 10 起）。

## 注意

- 不要在 `my-ai-platform` 插件仓库里跑 project-init
- review 默认只报告，不直接改代码
```

## A.2 session-start.sh

路径：`plugins/base-plugin/hooks/session-start.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
PLUGIN_ROOT="$(cd "${SCRIPT_DIR}/.." && pwd)"

plugin_md=$(cat "${PLUGIN_ROOT}/PLUGIN.md" 2>/dev/null || echo "base-plugin: PLUGIN.md missing")

escape_for_json() {
  local s="$1"
  s="${s//\\/\\\\}"
  s="${s//\"/\\\"}"
  s="${s//$'\n'/\\n}"
  s="${s//$'\r'/\\r}"
  s="${s//$'\t'/\\t}"
  printf '%s' "$s"
}

body=$(escape_for_json "$plugin_md")
session_context="<base-plugin-context>\nmy-platform / base-plugin loaded.\n\n${body}\n</base-plugin-context>"

if [ -n "${CLAUDE_PLUGIN_ROOT:-}" ] && [ -z "${COPILOT_CLI:-}" ]; then
  printf '{\n  "hookSpecificOutput": {\n    "hookEventName": "SessionStart",\n    "additionalContext": "%s"\n  }\n}\n' "$session_context"
else
  printf '{\n  "additionalContext": "%s"\n}\n' "$session_context"
fi

exit 0
```

## A.3 hooks.json

路径：`plugins/base-plugin/hooks/hooks.json`

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup|resume",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/hooks/session-start.sh",
            "async": false
          }
        ]
      }
    ]
  }
}
```

---

# 附录 B：验收命令速查

| 场景 | 命令 |
|------|------|
| 安装/更新插件 | `/plugin install base-plugin@my-platform` |
| 本地 marketplace | `/plugin marketplace add /Users/hsren2/iflytek/myworkspace/my-ai-platform` |
| 审查 | `/base-plugin:code-review` |
| 项目 init | `/base-plugin:project-init` |
| OpenSpec | `/opsx:propose "..."` → `/opsx:apply` → `/opsx:archive` |
| 手动测 hook | `CLAUDE_PLUGIN_ROOT=plugins/base-plugin bash plugins/base-plugin/hooks/session-start.sh` |
| 看缓存版本 | `ls ~/.claude/plugins/cache/my-platform/base-plugin/` |

---

## 你现在的行动（按顺序）

1. **Day 0.6**（30 min）：补全 `docs/dogfood-notes.md`
2. **Day 8**（2–3 h）：按附录 A 创建三个文件 → bump 0.1.2 → 验证
3. 完成后把本文 Day 0.6、Day 8 勾选 `[x]`，进度快照改为 Day 9

需要帮助时发送：**「Day N 完成」** 或粘贴报错原文。

---

*详细版 2.0 · hsren · my-ai-platform*
