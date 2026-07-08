# 跨平台安装与调用

本 Skill 遵循 [Agent Skills](https://agentskills.io) 规范（`SKILL.md` + 可选 reference 文件），**不绑定单一 IDE**。

## 兼容客户端

| 客户端 | 个人级路径 | 项目级路径 | 调用方式 |
| --- | --- | --- | --- |
| **Cursor** | `~/.cursor/skills/cn-economics-thesis-workflow-skill` | `.cursor/skills/cn-economics-thesis-workflow-skill` | `/cn-economics-thesis-workflow-skill` 或 `@cn-economics-thesis-workflow-skill` |
| **Codex** | `~/.codex/skills/cn-economics-thesis-workflow-skill` | 视客户端配置 | 对话中说明「按经济学论文工作流 Phase 0 开始」 |
| **WorkBuddy / CodeBuddy** | `~/.workbuddy/skills/` 或 `~/.codebuddy/skills/` | `.codebuddy/skills/` | trigger 自动激活或 @skill |
| **Claude Code** | `~/.claude/skills/cn-economics-thesis-workflow-skill` | `.claude/skills/` | `@cn-economics-thesis-workflow-skill` |
| **OpenCode** | `~/.config/opencode/skills/` | 项目 `opencode.jsonc` 引用 | 见本仓库 `opencode.jsonc` |
| **通用 Agent** | `~/.agents/skills/cn-economics-thesis-workflow-skill` | `.agents/skills/` | 客户端扫描 `SKILL.md` 后按 description 触发 |

## 安装

### git clone（推荐）

```bash
REPO="https://github.com/<YOUR_USERNAME>/cn-economics-thesis-workflow-skill.git"
NAME="cn-economics-thesis-workflow-skill"

# 任选目标路径（可多装）
git clone "$REPO" ~/.cursor/skills/$NAME
git clone "$REPO" ~/.codex/skills/$NAME
git clone "$REPO" ~/.workbuddy/skills/$NAME
git clone "$REPO" ~/.agents/skills/$NAME
```

### Skills CLI

```bash
npx skills add <YOUR_USERNAME>/cn-economics-thesis-workflow-skill -g -y
```

安装后**重启客户端或新开 Agent 会话**。

## 调用模板

**全流程（新论文）：**

```text
按 cn-economics-thesis-workflow-skill 从 Phase 0 开始。
我是 L2 水平。研究：Y=…，X=…，数据：已有 CSV / 需推荐来源。
计量全部用 Python（statsmodels + linearmodels），不用 Stata。
```

**从中间 Phase 续作：**

```text
按经济学论文工作流，从 Phase 4 继续。
项目目录：/path/to/thesis-project
已有 cleaned/dataset.parquet，变量 Y/X/控制变量已确定。
```

**仅排版：**

```text
按工作流 Phase 9，用 python-docx 路线。
回归表在 output/tables/，请生成 thesis/终稿.docx。
```

## 项目工作目录

Skill 安装在用户 skills 目录；**论文项目**应在独立文件夹初始化（Phase 0.2 结构）：

```
thesis-project/
├── data/raw/
├── cleaned/
├── scripts/econometrics/
├── output/tables/  output/figures/
├── thesis/
├── references/
└── log/
```

在 Codex / Cursor 中先将 Agent **工作区根目录**切换到 `thesis-project/`，再执行各 Phase。

## OpenCode 配置示例

见仓库根目录 [`opencode.jsonc`](opencode.jsonc)，将 skills 路径合并到项目配置即可。
