# Phase 5：内生性处理


> **关键设计**：Phase 5 处理内生性后所确立的模型框架，将被 Phase 6-8 继承使用。所有的后续回归都基于处理内生性后的模型，不退回 OLS。

### Step 5.1：内生性来源诊断

AI 从三个维度汇合识别内生性来源：

```
内生性诊断报告
═══════════════════════════════════════

1. 遗漏变量偏差
   • 可能遗漏的重要因素：{变量列表}
   • 方向判断：{遗漏变量与 X 和 Y 的相关方向}
   • 影响：{高估/低估} X 的效应

2. 反向因果
   • X → Y 还是 Y → X？
   • 经济逻辑判断：{具体分析}
   • 可能存在的原因：{具体机制}

3. 测量误差
   • X 的测量方式：{当前测量}
   • 可能的误差来源：{具体描述}
   • 误差类型：{经典 / 非经典}
```

### Step 5.2：方法推荐

AI 推荐内生性处理方法：

| 场景 | 推荐方法 | 适用前提 |
|------|----------|----------|
| 遗漏变量 | **工具变量 (IV-2SLS)** | 找到满足外生性和相关性的工具变量 |
| 自选择偏误 | **PSM** / **Heckman 两步法** | 足够的可观测变量构造倾向得分 |
| 样本选择偏误 | **Heckman 选择模型** | 有排他性约束变量 |
| 面板数据内生性 | **系统 GMM** / **差分 GMM** | T 相对较小，N 较大 |
| 自然实验/准实验 | **DID** / **断点回归 (RDD)** | 有外生政策冲击或分配阈值 |

### Step 5.3：数据缺口检查

AI 检查识别策略所需的数据是否就绪：

```
数据缺口检查
─────────────────────────────────

推荐方法：{IV / PSM / Heckman / GMM / DID / RDD}

所需额外数据/变量：
  □ {变量1} — 当前状态：{已有/需要构造/需用户提供}
  □ {变量2} — 当前状态：{已有/需要构造/需用户提供}
  ...

若需用户提供新数据：
  • 数据描述：{具体需要什么样的数据}
  • 推荐来源：{建议去哪里获取}
  • 格式要求：{dta / xlsx / csv}
─────────────────────────────────
```

### Step 5.4：额外变量推荐

如果需要额外变量，AI 推荐具体的构造方案：

```
变量构造方案
─────────────────────────────────

变量1（工具变量/排他性约束变量）：
  • 定义：{变量经济含义}
  • 构造方法：{如何从原始数据构造}
  • 预期相关性：{与 X 相关的原因}
  • 预期外生性：{与 Y 不直接相关的原因}
  • 文献依据：{哪些文章用了类似 IV}

变量2：
  ...
─────────────────────────────────
```

用户确认后，AI 生成构造代码或指导用户准备数据。

### Step 5.5：写 Python 脚本

```
内生性处理脚本（scripts/econometrics/endogeneity.py）
═══════════════════════════════════════

# 本脚本产出的模型框架将在 Phase 6-8 中被继承

# ── IV-2SLS 示例（linearmodels）──
from linearmodels.iv import IV2SLS
# formula: Y ~ 1 + X + controls + [X ~ Z]  （Z 为工具变量）
# 或 PanelOLS + 工具变量

# ── PSM 示例 ──
# sklearn LogisticRegression 估计倾向得分 → 匹配 → 回归

# ── DID 示例 ──
# 构造 treat×post 交互项 → PanelOLS

# ── 输出 ──
# output/tables/endogeneity.csv
```

```python
# scripts/econometrics/endogeneity.py（IV-2SLS 示例）
from linearmodels.iv import IV2SLS
import pandas as pd
from econ_utils import load_dataset, export_regression_table

df = load_dataset("cleaned/dataset.parquet")
formula = "Y ~ 1 + X + ctrl1 + ctrl2 + [X ~ Z]"  # Z 为工具变量
iv_model = IV2SLS.from_formula(formula, data=df).fit(cov_type="clustered", clusters=df["id"])
export_regression_table({"IV": iv_model}, "output/tables/endogeneity.csv")
print(iv_model.summary)
# 弱 IV 检验：iv_model.first_stage.diagnostics
```

### Step 5.6：结果解读

```
内生性处理结果解读
═══════════════════════════════════════

1. 第一阶段结果（如适用）
   • F 统计量 = {value}（>10 说明工具变量不弱）
   • 工具变量与 X 的相关性：{正/负} 显著

2. 第二阶段结果
   • 核心系数 β_IV = {value}，与基准 β_OLS = {value} 对比
   • 差异方向：{β_IV > β_OLS / β_IV < β_OLS}
   • 经济含义：{解读}

3. 模型诊断
   • 过度识别检验（若 IV 数量 > 内生变量数）：{p-value}
   • DWH 检验：{p-value}，{支持/不支持} 外生性假设
   • 弱工具变量检验：{Cragg-Donald / Kleibergen-Paap 统计量}

4. 最终结论
   • 内生性处理后 X 对 Y 的效应仍 {显著/不显著}
   • 基准回归的估计方向 {是/否} 稳健
```

### Step 5.7：确立最终模型框架

用户确认结果后，AI 记录**最终模型框架**，供 Phase 6-8 使用：

```
最终模型框架（Phase 6-8 继承）
═══════════════════════════════════════

数据集：cleaned/dataset.parquet
估计方法：{2SLS / PSM / Heckman / GMM / DID / RDD / PanelOLS}
核心解释变量：X
被解释变量：Y
控制变量：{控制变量列表}
固定效应：{个体/年份/行业等}
标准误聚类：{聚类层级}
工具变量（如有）：{IV list}

Phase 5 产出脚本：scripts/econometrics/endogeneity.py
Phase 5 产出表格：output/tables/endogeneity.csv
```

---

