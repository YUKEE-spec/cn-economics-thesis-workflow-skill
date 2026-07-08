# Phase 0：环境准备


### Step 0.1：依赖检测

AI 自动检测以下环境是否就绪：

```bash
# Python（核心）
python3 --version               # 需 ≥ 3.9
pip3 list 2>/dev/null | grep -iE "python-docx|openpyxl|pandas|numpy|matplotlib|seaborn|scipy|statsmodels|linearmodels"

# 计量包缺失时安装
pip3 install statsmodels linearmodels scipy seaborn
# 可选：高维固定效应
pip3 install pyfixest

# Node.js（排版 B 路线需要）
node --version                  # 需 ≥ 18
npm list docx 2>/dev/null || npm list -g docx 2>/dev/null

# uv（可选，推荐用于隔离环境）
uv --version 2>/dev/null
```

缺失项由 AI 提示安装命令。计量分析**不要求** Stata。

### Step 0.2：项目文件夹初始化

在工作目录下自动创建以下结构：

```
project_root/
├── data/
│   └── raw/                    # 原始数据（只读）
├── cleaned/                    # 清洗后数据（dataset.parquet / dataset.csv）
├── scripts/
│   ├── econometrics/           # 计量回归脚本（Phase 4-8）
│   │   ├── baseline.py
│   │   ├── endogeneity.py
│   │   ├── robustness.py
│   │   ├── mechanism.py
│   │   └── heterogeneity.py
│   └── econ_utils.py           # 共享工具：读数据、导出回归表、缩尾等
├── output/
│   ├── tables/                 # 回归表格（.csv，Phase 9 插入 Word）
│   └── figures/                # 图形（.png / .pdf）
├── thesis/                     # 论文 docx 输出目录
├── references/                 # 用户下载的 PDF 文献
├── log/                        # 运行日志
├── .gitignore
└── README.md
```

### Step 0.3：Python 计量环境初始化

AI 自动完成以下初始化（无需 MCP）：

1. **检测并安装计量包**：`statsmodels`、`linearmodels`（必需）；`pyfixest`（可选，高维 FE）
2. **创建 `scripts/econ_utils.py`**，至少包含：
   - `load_dataset(path)` — 读取 `cleaned/dataset.parquet` 或 `.csv` / `.dta`
   - `export_regression_table(results, path)` — 导出 CSV 回归表
   - `winsorize(series, lower, upper)` — 缩尾处理
   - `describe_panel(df, entity_col, time_col)` — 面板结构检查
3. **冒烟测试**：加载数据、跑一个简单 OLS，确认环境可用

```bash
python3 -c "import statsmodels, linearmodels; print('econometrics OK')"
python3 scripts/econometrics/baseline.py --smoke-test  # AI 首次创建后运行
```

> **执行方式**：Phase 4-8 中 AI 编写 Python 脚本后，用 Shell 运行 `python3 scripts/econometrics/<name>.py`，读取 stdout 和 `output/tables/` 中的 CSV 进行解读。不要使用 Stata 或 do file。

### Step 0.4：参考范文读取

询问用户是否提供参考范文 `.docx`：

```
是否有一篇参考范文 .docx 可以提供？
- 范文用于提取格式模板（样式、字体、字号、行距、页边距）
- 范文也用于参考写作风格（章节组织、论证方式、语言习惯）
- 范文放在 thesis/ 目录下即可
```

如用户提供，AI 使用 `python-docx` 读取范文的样式定义并应用到 Phase 9 输出。

### Step 0.5：用户水平确认

```
请选择你的水平，这将影响后续环节的详细程度：
  L1. 新手——第一次做实证论文，希望有引导和概念解释
  L2. 有基础——上过计量课，做过基本回归
  L3. 进阶——熟悉高级计量方法，追求深度
```

确认后存入上下文变量 `{{user_level}}`，后续所有决策按此自适应。

---

