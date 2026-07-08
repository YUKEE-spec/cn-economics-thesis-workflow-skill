# Phase 8：异质性分析


### Step 8.1：自动扫描分组维度

AI 自动扫描数据结构中可用的分类/分组变量：

```
数据结构中可用分组变量：
─────────────────────────────────
1. {变量名} — {描述} — {类别数} 个类别
   示例类别：{A, B, C}
   经济含义：{代表什么层面差异}

2. {变量名} — {描述} — {类别数} 个类别
   ...

3. {变量名（连续变量可二分）} — {描述}
   建议分组方式：{按中位数/均值/三分位分组}
   ...
─────────────────────────────────
```

### Step 8.2：维度推荐

AI 基于文献和逻辑推荐 3-5 个分组维度：

```
推荐异质性分析维度
═══════════════════════════════════════

维度 1：{分类依据}
  • 理论依据：{为什么在这个维度上可能存在异质性}
  • 预期：{A 组效应大于 B 组}

维度 2：{分类依据}
  • 理论依据：{为什么在这个维度上可能存在异质性}
  • 预期：{具体方向}

维度 3：{分类依据}
  • 理论依据：{为什么在这个维度上可能存在异质性}
  • 预期：{具体方向}

（可选）维度 4-5：{额外建议}
```

用户选择 2-3 个维度。

### Step 8.3：写 Python 脚本

```python
# scripts/econometrics/heterogeneity.py
"""异质性分析 — 分组回归 + 交互项检验"""
import pandas as pd
from linearmodels.panel import PanelOLS
from econ_utils import load_dataset, export_regression_table

df = load_dataset("cleaned/dataset.parquet")
df = df.set_index(["id", "year"])

results = {}
for label, sub in [("子组A", df[df["group"] == "A"]), ("子组B", df[df["group"] == "B"])]:
    mod = PanelOLS(sub["Y"], sub[["X"] + CONTROLS], entity_effects=True, time_effects=True)
    results[label] = mod.fit(cov_type="clustered", cluster_entity=True)

# ── 组间差异：引入 X × group 交互项 ──
df["X_group"] = df["X"] * (df["group"] == "B")
# ...

export_regression_table(results, "output/tables/heterogeneity.csv")
```

### Step 8.4：结果解读

```
异质性分析结果
═══════════════════════════════════════

维度 1：{Group variable}
  • A 组：β = {value}，{p-value}，样本量 N = {n}
  • B 组：β = {value}，{p-value}，样本量 N = {n}
  • 组间差异检验：{Chi2/p-value}
  • 结论：{解读}

维度 2：{Group variable}
  • ...

整体解读：
  {X 的效应在哪些群体中更强/更弱的总结性结论}
  {政策含义：针对性的建议}
```

---

