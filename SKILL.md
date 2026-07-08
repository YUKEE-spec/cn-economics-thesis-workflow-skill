---
name: cn-economics-thesis-workflow-skill
description: >-
  中文经济学实证论文全流程 Agent Skill（Python 计量版）。9 阶段交互式工作流：选题文献、
  数据变量、基准回归、内生性、稳健性、机制、异质性、撰写排版。使用 statsmodels/linearmodels，
  无需 Stata。跨平台：Cursor、Codex、WorkBuddy、Claude Code、OpenCode、通用 Agent Skills。
  Use for 经济学论文、实证分析、计量回归、毕业论文、cn-economics-thesis-workflow.
---

# 经济学实证论文工作流（Python 版）

## 版本

- **v2.0**（当前）：模块化 reference、跨平台适配、Agent 能力说明
- **v1.0**：Python 计量版 fork 自 [ZehChou/cn-economics-thesis-workflow-skill](https://github.com/ZehChou/cn-economics-thesis-workflow-skill)

## Purpose

引导用户**分阶段交互**完成中文经济学实证学位论文：选题 → 文献 → 数据 → Python 计量（Phase 4–8）→ `.docx` 成文（Phase 9）。

> **计量引擎**：`pandas` + `statsmodels` + `linearmodels`（可选 `pyfixest`）。**不依赖 Stata**。

## 首次启动（必读）

1. **确认平台与能力**：读 [reference-platform.md](reference-platform.md)、[reference-agent-capabilities.md](reference-agent-capabilities.md)
2. **确认用户水平**：L1 新手 / L2 有基础 / L3 进阶 → 存入 `{{user_level}}`
3. **确认入口 Phase**：新用户从 Phase 0；已有数据可从 Phase 3；仅润色/排版从 Phase 9
4. **输出《阶段确认单》**（场合、Y/X、数据状态、目标 Phase、当前阻塞项）→ 用户确认后再执行

## 工作流总览

```
Phase 0 → 1 → 2 → 3 → 4 → 5 → 6/7/8 → 9
环境    选题  综述  数据  基准  内生性  扩展检验  撰写排版
```

| Phase | 主题 | 详解 |
| ---: | --- | --- |
| 0 | 环境准备 | [reference-phase-0.md](reference-phase-0.md) |
| 1 | 选题与文献定位 | [reference-phase-1.md](reference-phase-1.md) |
| 2 | 文献综述与理论框架 | [reference-phase-2.md](reference-phase-2.md) |
| 3 | 数据与变量 | [reference-phase-3.md](reference-phase-3.md) |
| 4 | 基准回归 | [reference-phase-4.md](reference-phase-4.md) |
| 5 | 内生性处理 | [reference-phase-5.md](reference-phase-5.md) |
| 6 | 稳健性检验 | [reference-phase-6.md](reference-phase-6.md) |
| 7 | 机制检验 | [reference-phase-7.md](reference-phase-7.md) |
| 8 | 异质性分析 | [reference-phase-8.md](reference-phase-8.md) |
| 9 | 论文撰写与排版 | [reference-phase-9.md](reference-phase-9.md) |

## 用户水平分档

| 档位 | AI 行为 |
| --- | --- |
| **L1** | 每阶段 mini 教学；推荐保守默认项；标注常见坑 |
| **L2** | 跳过概念；直接方案与代码 |
| **L3** | 前沿方法、识别假设深挖、更多自定义 |

## 阶段执行规则（全 Phase 适用）

- **先问再答再生成**：每 Phase 结束须用户确认再进入下一 Phase
- **只读当前 Phase 详解**：进入 Phase N 时加载 `reference-phase-N.md`，勿一次加载全部
- **阶段产物落盘**：脚本 → `scripts/`；表图 → `output/`；论文 → `thesis/`；日志 → `log/`
- **继承变量**：`{{user_level}}` `{{model_framework}}` `{{data_path}}` `{{thesis_outline}}` `{{citation_db}}`（见 [reference-rules.md](reference-rules.md)）
- **Phase 5 后**：稳健性/机制/异质性均继承 Phase 5 模型框架，不退回裸 OLS

## Agent 最低能力

| 能力 | 用途 |
| --- | --- |
| Shell | 运行 `python3 scripts/econometrics/*.py`、pip 检测 |
| 读写文件 | 数据、脚本、CSV 表、docx |
| 网络（Phase 1–2） | Semantic Scholar、开放获取文献 |

无 Shell 的纯聊天客户端：**仅能提供文字指导**，无法自动跑回归；须告知用户并给出可手动执行的脚本。

## 输出默认

- 语言：中文（摘要可中英双语）
- 不编造文献、数据、回归结果；无数据时用 `〔待补充〕` 或占位表
- 回归结果以 `output/tables/*.csv` 为准，论文引用与 CSV 一致
- Phase 9 默认 A 路线（python-docx）；有 Node 环境可选 B 路线

## 附录与规范

| 内容 | 文件 |
| --- | --- |
| 格式规范（easy-paper） | [reference-appendix-format.md](reference-appendix-format.md) |
| docx 辅助 API | [reference-appendix-docx-api.md](reference-appendix-docx-api.md) |
| Python 计量栈 | [reference-appendix-python.md](reference-appendix-python.md) |
| 错误处理、时间估计 | [reference-rules.md](reference-rules.md) |
| 跨平台安装与调用 | [reference-platform.md](reference-platform.md) |
| Agent 能力边界 | [reference-agent-capabilities.md](reference-agent-capabilities.md) |

## 外部参考

| 仓库 | 用途 |
| --- | --- |
| [ZehChou/cn-economics-thesis-workflow-skill](https://github.com/ZehChou/cn-economics-thesis-workflow-skill) | 上游工作流 |
| [easy-paper](https://github.com/Dawnfz-Lenfeng/easy-paper) | 排版参数 |
| [docx-skill-4-cn-paper](https://github.com/Gostyan/docx-skill-4-cn-paper) | Phase 9 B 路线 |
