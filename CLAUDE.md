# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

本文件在每次 Claude Code 启动时自动加载，包含所有 skill 说明与代码库指引。

---

## 代码库架构

这是薛记炒货相关工具和 Claude Code Skills 的集合，无构建系统或测试框架。

```
skills/
├── xuji-xlr-stats/
│   └── process.py                  # 独立 CLI 脚本（主要版本）
└── xuji-xlr-stats-skill/
    ├── SKILL.md                    # Skill 描述与调用协议
    └── scripts/
        ├── process.py              # Skill 内嵌版本（逻辑同上）
        └── process_passed.py       # 变体：以「通过状态=已通过」为完成口径
```

**两个脚本版本的区别：**
- `process.py`：默认口径，`完成状态 == '已完成'` 计为完成
- `process_passed.py`：考核口径，`通过状态 == '已通过'` 计为完成

**核心数据流（process.py）：**
1. `load_data()` — 读取三个 Excel 输入文件
2. `filter_data()` — 剔除停用账号 + 过滤非门店人员（正则 `^[A-Z]\d{3}` 匹配门店编码）+ 岗位白名单
3. `enrich_with_arch()` — 按机构编码精确匹配小区负责人，失败则降级到（分公司, 区域）匹配，再失败则查手动覆盖表
4. `build_*` 系列函数 — 构建各报表 DataFrame
5. `write_*` 系列函数 — 用 openpyxl 写出带样式的 Excel

**依赖：** `pandas`、`openpyxl`（无 requirements.txt，需手动安装）

---

## 运行脚本

```bash
# 安装依赖（如未安装）
pip install pandas openpyxl

# 运行学习率统计（主版本）
python skills/xuji-xlr-stats/process.py \
  "<项目地图情况表路径>" \
  "<员工账号管理路径>" \
  "<架构表路径>" \
  "<输出目录>"

# 运行考核通过口径版本
python skills/xuji-xlr-stats-skill/scripts/process_passed.py \
  "<项目地图情况表路径>" \
  "<员工账号管理路径>" \
  "<架构表路径>" \
  "<输出目录>"
```

正常有效人员数量在 2000–3000 之间；偏离此范围需检查输入文件是否正确。

---

## 需要手动更新的配置

`process.py` 中有两处需要随业务变化手动维护的字典：

```python
# 小区负责人 → 成功部 映射（人员调整时更新）
XQ_TO_BU = {
    '周亭': '合伙人成功一部',
    '赵丹': '合伙人成功二部',
    '岳辉': '合伙人成功三部',
    '王刚': '合伙人成功四部',
}

# 架构表未收录的区域手动覆盖（新区域开设时更新）
MANUAL_REGION_MAP = {
    ('陕西销售中心', '延安区'): '周亭',
    ('湖北销售中心', '孝感区'): '周亭',
    ('江西销售中心', '吉安区'): '赵丹',
}
```

`skills/xuji-xlr-stats-skill/scripts/process.py` 和 `process_passed.py` 中有相同字典，需同步更新。

---

## 文件处理 Skills

### Word 文档（.docx）
创建、读取、编辑 Word 文档时使用。触发词：Word doc、.docx、报告、备忘录、信件、模板。

**工具链：**
- 读取/分析：`pandoc` 或解压 XML
- 创建新文档：`docx-js`
- 编辑已有文档：解压 → 编辑 XML → 重新打包
- `.doc` 转 `.docx`：`python scripts/office/soffice.py --headless --convert-to docx document.doc`

**输出标准：** 专业字体（Arial/Times New Roman）、零公式错误、保留已有模板格式。

---

### PDF
读取、合并、拆分、创建、填表、加水印、OCR 时使用。触发词：.pdf、PDF。

```python
from pypdf import PdfReader, PdfWriter

reader = PdfReader("document.pdf")
print(f"Pages: {len(reader.pages)}")

writer = PdfWriter()
writer.append(reader)
with open("output.pdf", "wb") as f:
    writer.write(f)
```

---

### PPT（.pptx）
创建、读取、编辑演示文稿时使用。触发词：deck、slides、presentation、.pptx。

**工具链：**
- 读取：`python -m markitdown presentation.pptx`
- 编辑已有文件：解压编辑 XML
- 从零创建：`pptxgenjs`

---

### Excel（.xlsx）
创建、编辑、分析电子表格时使用。触发词：.xlsx、.csv、表格、spreadsheet。

**工具链：** pandas（数据分析）+ openpyxl（格式/公式）

```python
import pandas as pd
df = pd.read_excel('file.xlsx', sheet_name=None)  # 读所有 sheet

from openpyxl import Workbook
wb = Workbook()
ws = wb.active
ws['B2'] = '=SUM(A1:A10)'  # 用公式，不用 Python 硬算值
wb.save('output.xlsx')
```

**输出标准：**
- 始终用 Excel 公式代替 Python 计算硬编码值
- 零公式错误（#REF! / #DIV/0! / #VALUE! / #N/A / #NAME?）
- 公式写完后用 LibreOffice 重新计算：`python scripts/recalc.py output.xlsx`

---

### 前端设计（Frontend）
构建网页组件、页面、Dashboard、React 组件时使用。

**设计原则：** 选择明确的视觉方向（极简/极繁/复古/工业等）并精确执行。避免通用 AI 审美。

**技术规范：**
- 优先 Tailwind 核心 utility class
- React 组件用默认 export，无必填 props
- 可用库：lucide-react、recharts、d3、three.js、shadcn/ui

---

## 薛记学习率统计（xuji-xlr-stats）

触发词：学习率、完成情况统计、项目地图、东区统计、区域统计、催学名单

**脚本位置：** `skills/xuji-xlr-stats/process.py`

### 输入文件
| 文件特征 | 用途 |
|---------|------|
| 含「项目地图情况表」 | 学习数据主表 |
| 含「员工账号管理」 | 需剔除的停用账号 |
| 含「架构」 | 门店-区域-小区负责人映射 |

### 核心过滤规则
1. 剔除员工账号管理中的人员
2. 只保留部门名称为门店编码格式（字母+3位以上数字开头）
3. 岗位白名单：营业员、店经理、店经理助理、储备店经理、训练员、训练组长、销售专员、管理培训生、实习生

### 小区负责人映射
| 小区负责人 | 成功部 | 配色 |
|-----------|-------|------|
| 周亭 | 合伙人成功一部 | 苏州园林绿 #3A7D44 |
| 赵丹 | 合伙人成功二部 | 西湖碧色 #1B7A8C |
| 岳辉 | 合伙人成功三部 | 外滩琉金 #B8862A |
| 王刚 | 合伙人成功四部 | 紫金山紫 #6B3FA0 |

### 手动区域覆盖（架构表未收录）
- 陕西销售中心 延安区 → 周亭
- 湖北销售中心 孝感区 → 周亭
- 江西销售中心 吉安区 → 赵丹

### 完成口径
- 默认：`完成状态 == '已完成'`
- 用户指定时可切换为：`通过状态 == '已通过'`（使用 `process_passed.py`）

### 输出文件
1. 单项目完成情况表（人员明细，含通过状态列）
2. 区域学习率统计（按分公司+区域汇总）
3. 东区学习率统计（按直营/合伙+成功部汇总）
4. 催学名单（按四位小区负责人分 Sheet，城市配色）
5. 考核未通过名单（完成但未通过考核）

---

## FLAC 转 Apple Music（flac-to-apple-music）

触发词：FLAC 转换、Apple Music 导入、无法导入、歌曲名显示 track 02

**前置要求：** `brew install ffmpeg`

### 核心转换命令
```bash
cd <FLAC文件所在目录>
for f in *.flac; do
  title="${f#*-}"          # 去掉「艺人名-」前缀
  title="${title%.flac}"   # 去掉 .flac 后缀
  ffmpeg -y -i "$f" \
    -c:a alac \
    -c:v copy \
    -map_metadata 0 \
    -map 0 \
    -metadata title="$title" \
    -metadata artist="<艺人名>" \
    "${f%.flac}.m4a"
done
```

### 文件名格式适配
| 文件名格式 | title 提取方式 |
|-----------|--------------|
| `艺人名-歌曲名.flac` | `title="${f#*-}"; title="${title%.flac}"` |
| `01 歌曲名.flac` | `title="${f#* }"; title="${title%.flac}"` |
| `歌曲名.flac` | `title="${f%.flac}"` |

### 嵌入封面
```bash
for f in *.m4a; do
  ffmpeg -y -i "$f" -i cover.jpg \
    -map 0:a -map 1 -c:a copy -c:v copy \
    -metadata:s:v title="Album cover" \
    "covered_${f}"
  mv "covered_${f}" "$f"
done
```

### 导入 Apple Music
文件 → 导入（`⌘O`）→ 选择 .m4a 文件夹

### 常见问题
| 问题 | 解决 |
|------|------|
| 歌曲名显示 track 02 | 使用上方命令（含 `-metadata title=`） |
| 拖入无反应 | 改用菜单「文件→导入」 |
| Apple Music 打不开 FLAC | 需先转为 .m4a |
| Gatekeeper 拦截 | `xattr -rd com.apple.quarantine <目录>` |

---

## Skill Creator

触发词：制作 skill、创建 skill、优化 skill、改进 skill

### 流程
1. **确认意图**：这个 skill 要做什么、何时触发、输出什么格式
2. **访谈细节**：边界情况、输入输出格式、成功标准
3. **写 SKILL.md**：含 YAML frontmatter（name + description）+ Markdown 正文
4. **测试**：针对测试 prompt 执行 skill，让用户评估结果
5. **迭代**：根据反馈修改，重复测试

### SKILL.md 结构
```
skill-name/
├── SKILL.md          # 必须，含 YAML frontmatter
└── scripts/          # 可选，可执行脚本
└── references/       # 可选，参考文档
└── assets/           # 可选，模板/字体/图标
```

### Description 写法要点
- 同时描述「做什么」和「何时触发」
- 列举具体触发词和场景
- 稍微「推进式」：用「只要用户提到X就应使用」而非「可以使用」

### 打包
```bash
cd <skill父目录>
zip -r <skill-name>.skill <skill-name>/
```
