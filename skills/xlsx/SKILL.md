---
name: xlsx
description: "用于表格的创建、编辑与分析（支持公式、格式、数据分析与可视化）。当 Claude 需要处理电子表格（.xlsx、.xlsm、.csv、.tsv 等）时使用，包括：(1) 创建带公式与格式的新表，(2) 读取/分析数据，(3) 在保留公式的前提下修改现有表，(4) 在表格中做数据分析与可视化，(5) 重新计算公式。"
license: Proprietary. LICENSE.txt has complete terms
---

# 输出要求

## 所有 Excel 文件

### 公式零错误
- 交付的每个 Excel 模型必须做到公式错误为 0（#REF!、#DIV/0!、#VALUE!、#N/A、#NAME?）

### 保留既有模板（更新模板时）
- 修改文件时先研究并严格匹配现有格式、样式与约定
- 对已经有成熟格式的文件，不要强行套用统一格式标准
- 既有模板约定永远优先于本指南

## 财务模型

### 配色编码标准
除非用户或既有模板另有说明

#### 行业通用颜色约定
- **蓝色文字（RGB: 0,0,255）**：手工输入值，以及用户会为情景分析调整的数字
- **黑色文字（RGB: 0,0,0）**：所有公式与计算结果
- **绿色文字（RGB: 0,128,0）**：同一工作簿内跨工作表的引用链接
- **红色文字（RGB: 255,0,0）**：引用外部文件的链接
- **黄色底色（RGB: 255,255,0）**：需要关注的关键假设或待更新单元格

### 数字格式标准

#### 必须遵守的格式规则
- **年份**：以文本串显示（例如 “2024”，不要显示为 “2,024”）
- **货币**：使用 $#,##0；并在表头始终标注单位（例如 “Revenue ($mm)”）
- **零值**：用格式把所有 0 显示为 “-”，包括百分比（例如 “$#,##0;($#,##0);-”）
- **百分比**：默认 0.0%（保留 1 位小数）
- **倍数**：估值倍数用 0.0x（如 EV/EBITDA、P/E）
- **负数**：用括号 (123)，不要用 -123

### 公式构造规则

#### 假设放置位置
- 所有假设（增速、毛利率、倍数等）必须放在单独的假设单元格中
- 公式中使用单元格引用，避免硬编码常量
- 例如：用 =B5*(1+$B$6)，不要用 =B5*1.05

#### 避免公式错误
- 核对所有单元格引用是否正确
- 检查范围是否存在 off-by-one 错误
- 确保所有预测期的公式一致
- 用边界情况测试（0 值、负数）
- 确保没有非预期的循环引用

#### 硬编码值的来源标注要求
- 在单元格批注中，或在表格末尾旁边的单元格标注来源。格式："Source: [System/Document], [Date], [Specific Reference], [URL if applicable]"
- 示例：
  - "Source: Company 10-K, FY2024, Page 45, Revenue Note, [SEC EDGAR URL]"
  - "Source: Company 10-Q, Q2 2025, Exhibit 99.1, [SEC EDGAR URL]"
  - "Source: Bloomberg Terminal, 8/15/2025, AAPL US Equity"
  - "Source: FactSet, 8/20/2025, Consensus Estimates Screen"

# XLSX 创建、编辑与分析

## 概述

用户可能会要求你创建、编辑或分析 .xlsx 文件内容。不同任务对应不同工具与工作流。

## 重要要求

**重新计算公式需要 LibreOffice**：可假设环境已安装 LibreOffice，用 `recalc.py` 脚本重算公式值。该脚本首次运行会自动配置 LibreOffice。

## 读取与分析数据

### 使用 pandas 做数据分析
进行数据分析、可视化与基础操作时，使用 **pandas**（提供强大的数据处理能力）：

```python
import pandas as pd

# Read Excel
df = pd.read_excel('file.xlsx')  # Default: first sheet
all_sheets = pd.read_excel('file.xlsx', sheet_name=None)  # All sheets as dict

# Analyze
df.head()      # Preview data
df.info()      # Column info
df.describe()  # Statistics

# Write Excel
df.to_excel('output.xlsx', index=False)
```

## Excel 文件工作流

## 关键：使用公式，不要硬编码数值

**始终使用 Excel 公式，而不是在 Python 中算出结果后写死到单元格里。**这样表格才能保持可更新与可重算。

### ❌ WRONG - Hardcoding Calculated Values
```python
# Bad: Calculating in Python and hardcoding result
total = df['Sales'].sum()
sheet['B10'] = total  # Hardcodes 5000

# Bad: Computing growth rate in Python
growth = (df.iloc[-1]['Revenue'] - df.iloc[0]['Revenue']) / df.iloc[0]['Revenue']
sheet['C5'] = growth  # Hardcodes 0.15

# Bad: Python calculation for average
avg = sum(values) / len(values)
sheet['D20'] = avg  # Hardcodes 42.5
```

### ✅ CORRECT - Using Excel Formulas
```python
# Good: Let Excel calculate the sum
sheet['B10'] = '=SUM(B2:B9)'

# Good: Growth rate as Excel formula
sheet['C5'] = '=(C4-C2)/C2'

# Good: Average using Excel function
sheet['D20'] = '=AVERAGE(D2:D19)'
```

该原则适用于所有计算：合计、百分比、比率、差值等。源数据变化后，表格应能自行重新计算。

## 通用流程
1. **选择工具**：数据用 pandas；公式/格式用 openpyxl
2. **创建/加载**：新建工作簿或加载已有文件
3. **修改**：新增/编辑数据、公式与格式
4. **保存**：写回文件
5. **重新计算公式（若使用公式则必须）**：使用 recalc.py 脚本
   ```bash
   python recalc.py output.xlsx
   ```
6. **检查并修复错误**：
   - 脚本会返回包含错误详情的 JSON
   - 若 `status` 为 `errors_found`，查看 `error_summary` 了解具体错误类型与位置
   - 修复后再次重算
   - 常见错误：
     - `#REF!`：无效单元格引用
     - `#DIV/0!`：除零
     - `#VALUE!`：公式中数据类型不匹配
     - `#NAME?`：无法识别的函数/名称

### 创建新的 Excel 文件

```python
# Using openpyxl for formulas and formatting
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment

wb = Workbook()
sheet = wb.active

# Add data
sheet['A1'] = 'Hello'
sheet['B1'] = 'World'
sheet.append(['Row', 'of', 'data'])

# Add formula
sheet['B2'] = '=SUM(A1:A10)'

# Formatting
sheet['A1'].font = Font(bold=True, color='FF0000')
sheet['A1'].fill = PatternFill('solid', start_color='FFFF00')
sheet['A1'].alignment = Alignment(horizontal='center')

# Column width
sheet.column_dimensions['A'].width = 20

wb.save('output.xlsx')
```

### 编辑现有 Excel 文件

```python
# Using openpyxl to preserve formulas and formatting
from openpyxl import load_workbook

# Load existing file
wb = load_workbook('existing.xlsx')
sheet = wb.active  # or wb['SheetName'] for specific sheet

# Working with multiple sheets
for sheet_name in wb.sheetnames:
    sheet = wb[sheet_name]
    print(f"Sheet: {sheet_name}")

# Modify cells
sheet['A1'] = 'New Value'
sheet.insert_rows(2)  # Insert row at position 2
sheet.delete_cols(3)  # Delete column 3

# Add new sheet
new_sheet = wb.create_sheet('NewSheet')
new_sheet['A1'] = 'Data'

wb.save('modified.xlsx')
```

## 重新计算公式

openpyxl 创建或修改的 Excel 文件会把公式保存为字符串，但不包含计算后的数值。使用提供的 `recalc.py` 脚本重算：

```bash
python recalc.py <excel_file> [timeout_seconds]
```

示例：
```bash
python recalc.py output.xlsx 30
```

脚本会：
- 首次运行时自动配置 LibreOffice 宏
- 重算所有工作表中的全部公式
- 扫描所有单元格的 Excel 错误（#REF!、#DIV/0! 等）
- 返回包含错误位置与数量的 JSON
- 同时支持 Linux 与 macOS

## 公式验证清单

用于快速确保公式正确运行的检查项：

### 核心验证
- [ ] **抽查 2–3 个引用**：在铺开全模型前先验证引用取值正确
- [ ] **列映射**：确认 Excel 列号对应正确（例如列 64 = BL，而不是 BK）
- [ ] **行偏移**：记住 Excel 行号从 1 开始（DataFrame 第 5 行 = Excel 第 6 行）

### 常见坑
- [ ] **NaN 处理**：用 `pd.notna()` 检查空值
- [ ] **靠右列**：FY 数据常在 50+ 列
- [ ] **多处匹配**：搜索所有出现位置，而不只看第一个
- [ ] **除零**：公式里用 `/` 前先检查分母（#DIV/0!）
- [ ] **错误引用**：核对所有引用是否指向目标单元格（#REF!）
- [ ] **跨表引用**：跨工作表用正确格式（Sheet1!A1）

### 公式测试策略
- [ ] **从小范围开始**：先在 2–3 个单元格测试再大范围应用
- [ ] **验证依赖**：检查公式引用的单元格都存在
- [ ] **测试边界情况**：包含 0、负数、超大数值

### 解读 recalc.py 输出
脚本会返回包含错误详情的 JSON：
```json
{
  "status": "success",           // or "errors_found"
  "total_errors": 0,              // Total error count
  "total_formulas": 42,           // Number of formulas in file
  "error_summary": {              // Only present if errors found
    "#REF!": {
      "count": 2,
      "locations": ["Sheet1!B5", "Sheet1!C10"]
    }
  }
}
```

## 最佳实践

### 库的选择
- **pandas**：适合数据分析、批量操作与简单导出
- **openpyxl**：适合复杂格式、公式与 Excel 特性

### 使用 openpyxl 的注意事项
- 单元格索引从 1 开始（row=1, column=1 对应 A1）
- 读取计算值可用 `data_only=True`：`load_workbook('file.xlsx', data_only=True)`
- **警告**：以 `data_only=True` 打开后再保存，会把公式替换为数值并永久丢失
- 大文件：读用 `read_only=True`，写用 `write_only=True`
- 公式会被保留但不会自动计算——用 recalc.py 更新计算值

### 使用 pandas 的注意事项
- 指定数据类型避免推断问题：`pd.read_excel('file.xlsx', dtype={'id': str})`
- 大文件仅读特定列：`pd.read_excel('file.xlsx', usecols=['A', 'C', 'E'])`
- 正确处理日期：`pd.read_excel('file.xlsx', parse_dates=['date_column'])`

## 代码风格指南
**重要**：生成 Excel 相关 Python 代码时：
- 代码尽量简短精炼，避免不必要的注释
- 避免冗长变量名与重复操作
- 避免不必要的 print 输出

**对 Excel 文件本身：**
- 对复杂公式或关键假设的单元格添加批注
- 对硬编码值记录数据来源
- 对关键计算与模型区块添加说明
