---
name: docx
description: "用于 .docx 专业文档的创建、编辑与分析（支持修订、批注、格式保留与文本提取）。当 Claude 需要处理专业文档（.docx）时使用，包括：(1) 新建文档，(2) 修改/编辑内容，(3) 处理修订，(4) 添加批注，以及其他任何文档任务。"
license: Proprietary. LICENSE.txt has complete terms
---

# DOCX 创建、编辑与分析

## 概述

用户可能会要求你创建、编辑或分析 .docx 文件内容。.docx 本质上是包含 XML 与其他资源的 ZIP 包，可以被解包后读取/编辑。不同任务对应不同工具与工作流。

## 工作流决策树

### 阅读/分析内容
使用下方“文本提取”或“原始 XML 访问”章节

### 新建文档
使用“创建新的 Word 文档”工作流

### 编辑现有文档
- **你自己的文档 + 简单改动**
  使用“基础 OOXML 编辑”工作流

- **他人的文档**
  使用 **“红线修订工作流（Redlining workflow）”**（推荐默认）

- **法律/学术/商务/政府类文档**
  使用 **“红线修订工作流（Redlining workflow）”**（必须）

## 阅读与分析内容

### 文本提取
如果只需要读取文档文本内容，使用 pandoc 将文档转换为 markdown。Pandoc 对结构保留支持很好，并可展示修订：

```bash
# Convert document to markdown with tracked changes
pandoc --track-changes=all path-to-file.docx -o output.md
# Options: --track-changes=accept/reject/all
```

### 原始 XML 访问
以下场景需要原始 XML：批注、复杂格式、文档结构、嵌入媒体与元数据。需要先解包文档并读取其 XML 内容。

#### 解包文件
`python ooxml/scripts/unpack.py <office_file> <output_directory>`

#### 关键文件结构
* `word/document.xml` - 主文档内容
* `word/comments.xml` - document.xml 引用的批注
* `word/media/` - 嵌入图片与媒体文件
* 修订使用 `<w:ins>`（插入）与 `<w:del>`（删除）标签

## 创建新的 Word 文档

从零创建 Word 文档时使用 **docx-js**，它支持用 JavaScript/TypeScript 生成 Word 文档。

### 工作流
1. **必须 - 全文阅读**：完整阅读 [`docx-js.md`](docx-js.md)（约 500 行）。**阅读时绝不要设置范围限制。**在开始创建文档前，先掌握详细语法、关键排版规则与最佳实践。
2. 使用 Document、Paragraph、TextRun 组件创建 JavaScript/TypeScript 文件（可默认依赖已安装；若没有，参考下方依赖章节）
3. 使用 Packer.toBuffer() 导出为 .docx

## 编辑现有 Word 文档

编辑现有 Word 文档时使用 **Document library**（用于 OOXML 操作的 Python 库）。该库会自动处理基础设施与常用操作方法；复杂场景可通过该库直接访问底层 DOM。

### 工作流
1. **必须 - 全文阅读**：完整阅读 [`ooxml.md`](ooxml.md)（约 600 行）。**阅读时绝不要设置范围限制。**先掌握 Document library API 与直接编辑文档 XML 的模式。
2. 解包文档：`python ooxml/scripts/unpack.py <office_file> <output_directory>`
3. 使用 Document library 编写并运行 Python 脚本（参考 ooxml.md 中的 “Document Library” 章节）
4. 打包回 .docx：`python ooxml/scripts/pack.py <input_directory> <office_file>`

Document library 同时提供常用操作的高层方法，以及复杂场景的 DOM 直访能力。

## 文档审阅的红线修订工作流（Redlining）

该工作流支持先用 markdown 规划全面的修订内容，再将其落实到 OOXML 中。**关键**：要实现完整修订，必须系统性地实现所有变更。

**分批策略**：将相关改动按 3–10 条为一批分组。这样既便于排错，也能保持效率。每批完成后先验证，再进入下一批。

**原则：最小且精确的编辑**
实现修订时，只标记真正发生变化的文本。重复未变文本会让审阅更困难且不专业。把替换拆成：[未变文本] + [删除] + [插入] + [未变文本]。对未变文本，通过从原文提取 `<w:r>` 并复用来保留原 run 的 RSID。

示例：将句子中的 “30 days” 改为 “60 days”：
```python
# BAD - Replaces entire sentence
'<w:del><w:r><w:delText>The term is 30 days.</w:delText></w:r></w:del><w:ins><w:r><w:t>The term is 60 days.</w:t></w:r></w:ins>'

# GOOD - Only marks what changed, preserves original <w:r> for unchanged text
'<w:r w:rsidR="00AB12CD"><w:t>The term is </w:t></w:r><w:del><w:r><w:delText>30</w:delText></w:r></w:del><w:ins><w:r><w:t>60</w:t></w:r></w:ins><w:r w:rsidR="00AB12CD"><w:t> days.</w:t></w:r>'
```

### 修订实施流程

1. **获取 markdown 表示**：将文档转换为 markdown 并保留修订：
   ```bash
   pandoc --track-changes=all path-to-file.docx -o current.md
   ```

2. **识别并分组变更**：审阅文档并识别所需的所有变更，将其组织成逻辑分批：

   **定位方法**（用于在 XML 中定位变更）：
   - 章节/标题编号（如 “Section 3.2”“Article IV”）
   - 若有编号则使用段落标识
   - 通过 grep 搜索带独特上下文的文本片段
   - 文档结构定位（如“第一段”“签名区块”）
   - **不要使用 markdown 行号**——它们无法映射到 XML 结构

   **分批方式**（每批 3–10 条相关变更）：
   - 按章节：如“批次 1：第 2 节修订”“批次 2：第 5 节更新”
   - 按类型：如“批次 1：日期更正”“批次 2：当事方名称变更”
   - 按复杂度：先做简单文本替换，再处理复杂结构性变更
   - 按顺序：如“批次 1：第 1–3 页”“批次 2：第 4–6 页”

3. **阅读文档并解包**：
   - **必须 - 全文阅读**：完整阅读 [`ooxml.md`](ooxml.md)（约 600 行）。**阅读时绝不要设置范围限制。**重点关注 “Document Library” 与 “Tracked Change Patterns” 章节。
   - **解包文档**：`python ooxml/scripts/unpack.py <file.docx> <dir>`
   - **记录建议的 RSID**：解包脚本会建议用于修订的 RSID。复制该 RSID 以供步骤 4b 使用。

4. **分批实现变更**：按章节/类型/相邻性将变更分组，并在单个脚本中一起实现。这样做：
   - 更易调试（批次越小越容易隔离问题）
   - 支持增量推进
   - 保持效率（每批 3–10 条通常最合适）

   **建议的分组方式：**
   - 按文档章节（如“第 3 节改动”“定义”“终止条款”）
   - 按变更类型（如“日期变更”“当事方名称更新”“法律术语替换”）
   - 按位置相邻（如“第 1–3 页改动”“文档前半部分改动”）

   For each batch of related changes:

   **a. 将文本映射到 XML**：在 `word/document.xml` 中 grep 相关文本，确认文本在 `<w:r>` 之间的拆分方式。

   **b. 编写并运行脚本**：使用 `get_node` 定位节点，实施变更后 `doc.save()`。参考 ooxml.md 的 **“Document Library”** 章节模式。

   **注意**：写脚本前务必立即 grep 一次 `word/document.xml`，以获得最新行号并核对文本内容。每次脚本运行后行号都会变化。

5. **打包文档**：所有批次完成后，将解包目录打包回 .docx：
   ```bash
   python ooxml/scripts/pack.py unpacked reviewed-document.docx
   ```

6. **最终验证**：对完整文档做全面检查：
   - 将最终文档转换为 markdown：
     ```bash
     pandoc --track-changes=all reviewed-document.docx -o verification.md
     ```
   - 验证所有变更是否正确应用：
     ```bash
     grep "original phrase" verification.md  # Should NOT find it
     grep "replacement phrase" verification.md  # Should find it
     ```
   - 检查是否引入了非预期变更


## 将文档转换为图片

如需可视化分析 Word 文档，可按两步将其转换为图片：

1. **DOCX 转 PDF**：
   ```bash
   soffice --headless --convert-to pdf document.docx
   ```

2. **PDF 页面转 JPEG 图片**：
   ```bash
   pdftoppm -jpeg -r 150 document.pdf page
   ```
   This creates files like `page-1.jpg`, `page-2.jpg`, etc.

Options:
- `-r 150`: Sets resolution to 150 DPI (adjust for quality/size balance)
- `-jpeg`: Output JPEG format (use `-png` for PNG if preferred)
- `-f N`: First page to convert (e.g., `-f 2` starts from page 2)
- `-l N`: Last page to convert (e.g., `-l 5` stops at page 5)
- `page`: Prefix for output files

指定范围示例：
```bash
pdftoppm -jpeg -r 150 -f 2 -l 5 document.pdf page  # Converts only pages 2-5
```

## 代码风格指南
**重要**：生成 DOCX 相关代码时：
- 代码要简洁
- 避免冗长变量名与重复操作
- 避免不必要的 print 输出

## 依赖

所需依赖（如缺失则安装）：

- **pandoc**: `sudo apt-get install pandoc` (for text extraction)
- **docx**: `npm install -g docx` (for creating new documents)
- **LibreOffice**: `sudo apt-get install libreoffice` (for PDF conversion)
- **Poppler**: `sudo apt-get install poppler-utils` (for pdftoppm to convert PDF to images)
- **defusedxml**: `pip install defusedxml` (for secure XML parsing)
