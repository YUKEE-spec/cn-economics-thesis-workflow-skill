# 附录 C：Python 计量栈配置与使用指南


### C.1 安装

```bash
pip3 install statsmodels linearmodels scipy seaborn pandas pyarrow
# 可选：高维固定效应
pip3 install pyfixest
```

推荐使用项目虚拟环境：

```bash
uv venv .venv && source .venv/bin/activate
uv pip install statsmodels linearmodels scipy seaborn pandas pyarrow python-docx
```

### C.2 Stata 与 Python 方法对照

| 任务 | Stata | Python |
|------|-------|--------|
| OLS + 聚类 SE | `reg Y X, vce(cluster id)` | `sm.OLS(...).fit(cov_type="cluster")` |
| 双向固定效应 | `reghdfe Y X, absorb(id year)` | `PanelOLS(..., entity_effects=True, time_effects=True)` |
| IV-2SLS | `ivreg2` / `ivreghdfe` | `linearmodels.iv.IV2SLS` |
| 缩尾 | `winsor2` | `econ_utils.winsorize()` 或 `df.quantile()` |
| 导出表格 | `esttab` → RTF | `export_regression_table()` → CSV |
| 读取 .dta | `use` | `pd.read_stata()` |

### C.3 脚本执行规范（Phase 4-8）

1. 所有计量脚本放在 `scripts/econometrics/`
2. 共享工具放在 `scripts/econ_utils.py`
3. 运行：`python3 scripts/econometrics/<script>.py`
4. 结果统一输出到 `output/tables/*.csv` 和 `log/`
5. 脚本失败时：读取 traceback，检查变量名、缺失值、面板索引，修复后重跑

### C.4 常用 Python 包

| 包 | 用途 |
|----|------|
| `statsmodels` | OLS、Logit、Probit、时间序列 |
| `linearmodels` | 面板 FE、IV-2SLS、GMM |
| `pyfixest` | 高维固定效应（可选，类似 reghdfe） |
| `sklearn` | PSM 倾向得分估计 |
| `pandas` / `pyarrow` | 数据处理与 parquet 存储 |

AI 在 Phase 4 首次运行前，应检测上述包是否安装，缺失则提示安装命令。

---

