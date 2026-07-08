# 附录 B：辅助函数 API 规约（源自 docx-skill-4-cn-paper）


以下接口是从 [docx-skill-4-cn-paper](https://github.com/Gostyan/docx-skill-4-cn-paper) 项目中提取的辅助函数体系。A 路线（Python）和 B 路线（Node.js）各自实现这组接口。

### B.1 文本类函数

```python
# ── Python 路线实现接口 ──
# 每个函数在下方给出原型签名

def heading1(doc, text: str) -> None:
    """一级标题（自动编号 第一章/第二章...）
       - 字体：黑体 15pt 居中
       - 前后间距：6pt / 3pt"""

def heading2(doc, text: str) -> None:
    """二级标题（自动编号 1.1/1.2...）
       - 字体：黑体 12pt 左对齐
       - 前后间距：6pt / 3pt"""

def heading3(doc, text: str) -> None:
    """三级标题（自动编号 1.1.1/1.1.2...）
       - 字体：黑体 10.5pt 左对齐"""

def body(doc, text: str) -> None:
    """正文段落
       - 自动检测文本中是否包含公式、引用标记
       - 首行缩进 2 字符
       - 字体：宋体 10.5pt
       - 行距：1.5 倍"""

def formula(doc, latex: str, tag: str = "") -> None:
    """居中公式 + 右对齐编号
       - A 路线：通过 latex2mathml 转换为 Word 公式
       - B 路线：通过 temml → MathML → OMML"""

def caption_figure(doc, text: str) -> None:
    """图题（下方居中）
       - 字体：黑体 9pt 居中"""

def caption_table(doc, text: str) -> None:
    """表题（上方居中）
       - 字体：黑体 9pt 居中"""

def note(doc, text: str) -> None:
    """图注/表注
       - 字体：宋体 9pt
       - 位于图/表下方"""

def abstract_cn(doc, text: str, keywords: list) -> None:
    """中文摘要 + 关键词
       - 摘要正文：宋体 10.5pt
       - 关键词格式："关键词：词1；词2；词3""""

def abstract_en(doc, text: str, keywords: list) -> None:
    """英文摘要 + 关键词"""
```

### B.2 表格类函数

```python
def three_line_table(doc, headers: list, rows: list, caption: str) -> None:
    """三线表
       - 顶线 1.5pt / 栏目线 0.75pt / 底线 1.5pt
       - 表头加粗，表中文字 9pt
       - 自动编号"""

def stat_table(doc, headers: list, rows: list, caption: str,
               note_text: str = "") -> None:
    """统计表格（描述性统计 / 相关性矩阵）
       - 继承三线表格式
       - 数字右对齐，文字左对齐"""
```

### B.3 图表类函数

```python
def insert_figure(doc, image_path: str, caption: str,
                  width: float = None) -> None:
    """插入图片
       - 居中显示
       - 自动编号图序
       - width 参数控制显示宽度（厘米）"""

def regression_table(doc, results_files: list, caption: str) -> None:
    """插入回归结果表
       - 从 output/tables/*.csv 读取（Python 回归导出）
       - 转换为 docx 三线表格式
       - 保留显著性星号标注"""
```

### B.4 排版类函数

```python
def page_setup(doc, margins: dict = None) -> None:
    """页面设置（A4、页边距、页眉页脚）"""

def toc(doc) -> None:
    """生成目录占位符（用户 Word 中更新域）
       - A 路线：插入 TOC 域代码
       - B 路线：同"""

def header_footer(doc, header_text: str = "",
                  page_number: bool = True) -> None:
    """页眉页脚 + 页码"""

def reference_list(doc, references: list) -> None:
    """参考文献列表（GB/T 7714）
       - 按拼音/字母排序
       - 字体 9pt 宋体"""

def cover_page(doc, title: str, author: str, **kwargs) -> None:
    """封面页（按学校/范文模板）"""
```

### B.5 路线差异对照

| 功能 | A 路线（Python） | B 路线（Node.js） |
|------|-----------------|-------------------|
| 库依赖 | `python-docx` | `docx` (npm) |
| 公式渲染 | `latex2mathml` 或 `python-docx` OMML | `temml` → MathML → `docx` OMML |
| 读取模板样式 | `docx.Document()` 直接读取 | `docx.Document()` 读取 |
| 辅助函数 | 自行实现上述接口 | 直接调用 `scripts/new_doc.js` 中的已实现函数 |
| 代码位置 | `scripts/generate_thesis.py` | `scripts/generate_thesis.js` |
| 运行命令 | `python scripts/generate_thesis.py` | `node scripts/generate_thesis.js` |

---

