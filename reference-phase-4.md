# Phase 4：基准回归


### Step 4.1：方法推荐

AI 基于数据结构推荐基准回归策略：

| 数据结构 | 推荐方法 | 说明 |
|----------|----------|------|
| 截面数据 | OLS | 线性回归，异方差稳健标准误 |
| 面板数据 | 固定效应 (FE) / 随机效应 (RE) | Hausman 检验决定 |
| 二值因变量 | Logit / Probit | 报告边际效应 |
| 计数数据 | Poisson / 负二项 | |
| 多期面板 | 双向固定效应 (TWFE) | 个体+时间固定效应 |

L1 用户：AI 简要解释所选方法的适用性
L2/L3 用户：AI 同时讨论模型假设和识别条件

### Step 4.2：全流程说明

在写 Python 脚本之前，AI 先向用户说明完整流程：

```
基准回归流程说明
═══════════════════════════════════════

1. 模型设定
   Y_it = α + β*X_it + γ*Controls_it + μ_i + λ_t + ε_it

   • β 是核心估计量，预期方向：{正/负}
   • 控制变量已包含：{控制变量列表}
   • 个体固定效应 μ_i：控制不随时间变化的不可观测因素
   • 时间固定效应 λ_t：控制年度宏观冲击
   • 标准误在 {个体/年份} 层面聚类

2. 回归序列
   (1) 仅 X：Y = α + β*X
   (2) 加入控制变量
   (3) 加入固定效应
   (4) 完整模型（最终基准）

3. 输出
   • 回归系数表（output/tables/baseline.csv）
   • 各模型 R²、F 统计量
   • 如有需要，输出边际效应图

确认后开始写 Python 脚本？
─────────────────────────────────
```

### Step 4.3：写 Python 回归脚本

AI 创建 `scripts/econometrics/baseline.py` 并运行。写脚本前先加载数据做冒烟检查：

```python
# scripts/econometrics/baseline.py
"""基准回归 — 生成时间：{date}"""
import pandas as pd
from linearmodels.panel import PanelOLS
import statsmodels.api as sm
from pathlib import Path
import sys
sys.path.append(str(Path(__file__).resolve().parents[1]))
from econ_utils import load_dataset, export_regression_table

DATA_PATH = "cleaned/dataset.parquet"
ENTITY_COL, TIME_COL = "id", "year"
Y, X = "Y", "X"
CONTROLS = ["ctrl1", "ctrl2"]  # 按实际变量替换

df = load_dataset(DATA_PATH)
df = df.set_index([ENTITY_COL, TIME_COL])

# Model 1: 仅核心变量（截面 OLS + 聚类标准误）
m1 = sm.OLS(df[Y], sm.add_constant(df[X])).fit(
    cov_type="cluster", cov_kwds={"groups": df.index.get_level_values(0)}
)

# Model 2: 加入控制变量
m2 = sm.OLS(df[Y], sm.add_constant(df[[X] + CONTROLS])).fit(
    cov_type="cluster", cov_kwds={"groups": df.index.get_level_values(0)}
)

# Model 3: 双向固定效应（TWFE）
m3 = PanelOLS(df[Y], df[[X] + CONTROLS], entity_effects=True, time_effects=True)
m3 = m3.fit(cov_type="clustered", cluster_entity=True)

export_regression_table(
    {"(1)": m1, "(2)": m2, "(3)": m3},
    "output/tables/baseline.csv",
    title="基准回归结果",
)
print(m3.summary)
```

运行：`python3 scripts/econometrics/baseline.py`

### Step 4.4：结果解读

AI 贴出回归结果后，逐项解读：

```
基准回归结果解读
═══════════════════════════════════════

1. 核心变量 X
   • 系数 β = {value}，在 {p-value} 水平上显著
   • 经济含义：X 每变化一个标准差，Y 变化 {effect_size} 个标准差
   • 与预期方向 {一致/不一致}

2. 控制变量
   • {控制变量1}：{显著方向和经济含义}
   • {控制变量2}：{显著方向和经济含义}
   ...

3. 模型拟合度
   • R² = {value}，F 统计量显著
   • 说明模型解释了 Y 变异的 {percent}

4. 检查点
   □ 系数方向与理论预期一致
   □ 核心系数在统计上显著
   ☐ p > 0.1 时：AI 应推荐改善方案（见下方）
```

### Step 4.5：显著性改善方案

若核心变量 p > 0.1，AI 按优先级推荐以下方案：

```
建议改善方案：
1. 检查数据量是否充足 → {样本量是否够}
2. 检查变量定义 → {是否有测量误差}
3. 考虑更高维固定效应 → {如行业×年份 FE}
4. 考虑更优标准误聚类 → {如行业层面聚类}
5. 检查离群值影响 → {缩尾处理}
6. 考虑非线性效应 → {引入平方项}
7. 更换估计方法 → {更换为更适合的方法}

请选择要尝试的方案（可多选）：A / B / C / ...
```

用户选择后，AI 更新 Python 脚本重新运行，直至用户对结果满意。

---

