# Phase 7：机制检验


### Step 7.1：研究假设回顾

AI 回顾 Phase 2 推导的研究假设：

```
待检验的机制假设：
  H1: X → Y（已在 Phase 4-6 验证）
  H2a: X → M1 → Y（中介效应）
  H2b: X → M2 → Y（中介效应）
  H3: Z 调节 X → Y 的关系
```

### Step 7.2：方法推荐

```
推荐机制检验方案
═══════════════════════════════════════

1. 中介效应（H2a / H2b）
   方法 A：Baron & Kenny 三步法 + Sobel 检验
   方法 B：Bootstrap 中介检验（推荐，500/1000 次）
   方法 C：江艇（2022）两步法（适合面板数据）

2. 调节效应（H3）
   Y = α + β1*X + β2*Z + β3*X×Z + γ*Controls + ε
   关注 β3 的符号和显著性

建议：{根据数据结构和假设类型推荐最佳方法}
```

### Step 7.3：中介变量回顾性检查

AI 检查 Phase 3 构造的机制变量是否就绪，或是否需要补充：

```
机制变量状态检查：
  □ M1（{变量名}）— {已有/需补充}
  □ M2（{变量名}）— {已有/需补充}
  □ Z（{调节变量}）— {已有/需补充}

如需补充，推荐构造方案：
  • {变量名}：{构造方法和计算说明}
```

### Step 7.4：写 Python 脚本

```python
# scripts/econometrics/mechanism.py
"""机制检验 — 中介效应 / 调节效应"""
import pandas as pd
import statsmodels.api as sm
from linearmodels.panel import PanelOLS
from econ_utils import load_dataset, export_regression_table

df = load_dataset("cleaned/dataset.parquet")
df = df.set_index(["id", "year"])

# ── 中介效应 Step 1：X → M ──
m_step1 = PanelOLS(df["M"], df[["X"] + CONTROLS], entity_effects=True, time_effects=True)
m_step1 = m_step1.fit(cov_type="clustered", cluster_entity=True)

# ── 中介效应 Step 2：X + M → Y ──
m_step2 = PanelOLS(df["Y"], df[["X", "M"] + CONTROLS], entity_effects=True, time_effects=True)
m_step2 = m_step2.fit(cov_type="clustered", cluster_entity=True)

# ── Sobel 检验（可手写或用 statsmodels 间接效应公式）──
# ...

export_regression_table({"X→M": m_step1, "X+M→Y": m_step2}, "output/tables/mechanism.csv")
```

### Step 7.5：结果解读

```
机制检验结果
═══════════════════════════════════════

H2a: X → M1 → Y
  • X → M1：系数 β = {value}，{显著/不显著}
  • M1 → Y（含 X）：系数 = {value}，{显著/不显著}
  • Sobel Z = {value}，p = {value}
  • {支持/不支持} H2a

H2b: X → M2 → Y
  • ...

H3: Z 的调节效应
  • 交互项系数 β3 = {value}，{显著/不显著}
  • 方向：{正向/负向} 调节
  • {支持/不支持} H3

机制总结：{X 通过什么渠道影响 Y 的整体结论}
```

---

