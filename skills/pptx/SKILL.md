---
name: pptx
description: "用于演示文稿（.pptx）的创建、编辑与分析。当 Claude 需要处理演示文稿时使用，包括：(1) 新建演示文稿，(2) 修改/编辑内容，(3) 处理版式布局，(4) 添加批注或演讲者备注，以及其他任何演示文稿任务。"
license: Proprietary. LICENSE.txt has complete terms
---

# PPTX 创建、编辑与分析

## 概述

用户可能会要求你创建、编辑或分析 .pptx 文件内容。.pptx 本质上是包含 XML 与其他资源的 ZIP 包，可以被解包后读取/编辑。不同任务对应不同工具与工作流。

## 阅读与分析内容

### 文本提取
如果只需要读取演示文稿的文字内容，将其转换为 markdown：

```bash
# Convert document to markdown
python -m markitdown path-to-file.pptx
```

### 原始 XML 访问
以下场景需要原始 XML：批注、演讲者备注、幻灯片版式、动画、设计元素与复杂格式。需要先解包演示文稿并读取原始 XML。

#### 解包文件
`python ooxml/scripts/unpack.py <office_file> <output_dir>`

**注意**：unpack.py 脚本位于项目根目录下的 `skills/pptx/ooxml/scripts/unpack.py`。若该路径不存在，使用 `find . -name "unpack.py"` 定位它。

#### 关键文件结构
* `ppt/presentation.xml` - 演示文稿元数据与幻灯片引用
* `ppt/slides/slide{N}.xml` - 单张幻灯片内容（slide1.xml、slide2.xml 等）
* `ppt/notesSlides/notesSlide{N}.xml` - 每张幻灯片的演讲者备注
* `ppt/comments/modernComment_*.xml` - 特定幻灯片的批注
* `ppt/slideLayouts/` - 幻灯片版式模板
* `ppt/slideMasters/` - 母版模板
* `ppt/theme/` - 主题与样式信息
* `ppt/media/` - 图片与其他媒体文件

#### 字体与颜色提取
**当给出要模仿的设计示例时**：务必先用以下方法分析演示文稿的字体与配色：
1. **读取主题文件**：查看 `ppt/theme/theme1.xml` 的颜色（`<a:clrScheme>`）与字体（`<a:fontScheme>`）
2. **抽样查看幻灯片内容**：检查 `ppt/slides/slide1.xml` 的真实字体使用（`<a:rPr>`）与颜色
3. **搜索模式**：用 grep 在所有 XML 中搜索颜色（`<a:solidFill>`、`<a:srgbClr>`）与字体引用

## **无模板**新建 PowerPoint 演示文稿

从零创建新演示文稿时，使用 **html2pptx** 工作流：先用 HTML 编写幻灯片，再转换为定位准确的 PowerPoint。

### 设计原则

**关键**：在制作任何演示文稿之前，先分析内容并选择合适的设计元素：
1. **考虑主题**：这份演示讲什么？它暗示了怎样的语气、行业与氛围？
2. **检查品牌**：若用户提到公司/组织，考虑其品牌色与视觉识别
3. **配色贴合内容**：选择能反映主题的颜色
4. **先说明方法**：写代码之前先解释你的设计选择

**要求**：
- ✅ 在写代码前，先说明“基于内容”的设计思路
- ✅ 只使用 web-safe 字体：Arial、Helvetica、Times New Roman、Georgia、Courier New、Verdana、Tahoma、Trebuchet MS、Impact
- ✅ 用字号、字重与颜色建立清晰层级
- ✅ 保证可读性：强对比、合适字号、干净对齐
- ✅ 保持一致性：跨幻灯片复用模式、间距与视觉语言

#### Color Palette Selection

**创造性地选色**：
- **跳出默认**：哪些颜色真正匹配这个主题？避免自动驾驶式选择。
- **多角度考虑**：主题、行业、情绪、能量水平、目标受众、品牌识别（如有）
- **敢于尝试**：意想不到的组合也可行——医疗不一定非绿，金融不一定非海军蓝
- **构建调色盘**：选择 3–5 个能协同工作的颜色（主色 + 辅色 + 点缀色）
- **保证对比**：文字必须在背景上清晰可读

**示例调色盘**（用于启发灵感：可直接选用、改造或自创）：

1. **经典蓝（Classic Blue）**：深海军蓝 (#1C2833)、石板灰 (#2E4053)、银灰 (#AAB7B8)、米白 (#F4F6F6)
2. **青绿与珊瑚（Teal & Coral）**：青绿 (#5EA8A7)、深青绿 (#277884)、珊瑚红 (#FE4447)、白 (#FFFFFF)
3. **大胆红（Bold Red）**：红 (#C0392B)、亮红 (#E74C3C)、橙 (#F39C12)、黄 (#F1C40F)、绿 (#2ECC71)
4. **暖粉（Warm Blush）**：藕紫 (#A49393)、浅粉 (#EED6D3)、玫瑰粉 (#E8B4B8)、奶油白 (#FAF7F2)
5. **勃艮第奢华（Burgundy Luxury）**：酒红 (#5D1D2E)、绛红 (#951233)、锈橙 (#C15937)、金 (#997929)
6. **深紫与祖母绿（Deep Purple & Emerald）**：紫 (#B165FB)、深蓝 (#181B24)、祖母绿 (#40695B)、白 (#FFFFFF)
7. **奶油白与森林绿（Cream & Forest Green）**：奶油白 (#FFE1C7)、森林绿 (#40695B)、白 (#FCFCFC)
8. **粉与紫（Pink & Purple）**：粉 (#F8275B)、珊瑚 (#FF574A)、玫瑰 (#FF737D)、紫 (#3D2F68)
9. **青柠与梅子（Lime & Plum）**：青柠 (#C5DE82)、梅子紫 (#7C3A5F)、珊瑚橙 (#FD8C6E)、蓝灰 (#98ACB5)
10. **黑金（Black & Gold）**：金 (#BF9A4A)、黑 (#000000)、米白 (#F4F6F6)
11. **鼠尾草与陶土（Sage & Terracotta）**：鼠尾草绿 (#87A96B)、陶土橙 (#E07A5F)、米白 (#F4F1DE)、炭灰 (#2C2C2C)
12. **炭灰与红（Charcoal & Red）**：炭灰 (#292929)、红 (#E33737)、浅灰 (#CCCBCB)
13. **高饱和橙（Vibrant Orange）**：橙 (#F96D00)、浅灰 (#F2F2F2)、炭灰 (#222831)
14. **森林绿（Forest Green）**：黑 (#191A19)、绿 (#4E9F3D)、深绿 (#1E5128)、白 (#FFFFFF)
15. **复古彩虹（Retro Rainbow）**：紫 (#722880)、粉 (#D72D51)、橙 (#EB5C18)、琥珀 (#F08800)、金 (#DEB600)
16. **复古大地（Vintage Earthy）**：芥末黄 (#E3B448)、鼠尾草绿 (#CBD18F)、森林绿 (#3A6B35)、米白 (#F4F1DE)
17. **海岸玫瑰（Coastal Rose）**：旧玫瑰 (#AD7670)、灰棕 (#B49886)、蛋壳白 (#F3ECDC)、灰绿 (#BFD5BE)
18. **橙与绿松石（Orange & Turquoise）**：浅橙 (#FC993E)、灰绿松石 (#667C6F)、白 (#FCFCFC)

#### 视觉细节选项

**几何图案**：
- 用对角分割线替代水平分割
- 非对称列宽（30/70、40/60、25/75）
- 文本标题旋转 90° 或 270°
- 图片使用圆形/六边形框
- 角落用三角形点缀
- 叠加形状制造纵深

**边框与框架处理**：
- 仅在一侧使用粗单色边框（10–20pt）
- 使用对比色双线边框
- 用“角括号”替代整框
- L 形边框（上+左 或 下+右）
- 标题下方使用下划线强调（3–5pt）

**字体处理**：
- 极端字号对比（72pt 大标题 vs 11pt 正文）
- 全大写标题 + 加宽字距
- 用超大展示字做编号分节
- 数据/统计/技术内容使用等宽字体（Courier New）
- 信息密度高时用窄体（Arial Narrow）
- 描边文字用于强调

**图表与数据样式**：
- 单色系图表 + 单一强调色突出关键数据
- 用横向条形图替代纵向
- 用点图替代柱状图
- 网格线极少或不使用
- 直接在图形元素上标注数据（不使用图例）
- 关键指标用超大数字

**布局创新**：
- 满版图片 + 文字叠加
- 侧边栏列（20–30% 宽）用于导航/上下文
- 模块化网格系统（3×3、4×4）
- Z 型或 F 型内容流
- 在色块上漂浮文本框
- 杂志风多栏排版

**背景处理**：
- 40–60% 页面面积用纯色块占据
- 渐变填充（仅竖向或对角）
- 分割背景（两色，对角或竖向）
- 边到边色带
- 将留白作为设计元素

### 布局建议
**制作包含图表或表格的幻灯片时：**
- **两栏布局（推荐）**：顶部用跨全宽标题，下方两栏——一栏放文字/要点，另一栏放重点内容（图表/表格）。更平衡且更利于阅读。可用不等宽 flexbox（例如 40%/60%）为不同内容类型优化空间。
- **全屏布局**：让重点内容（图表/表格）占满整页，以获得最大冲击力与可读性
- **禁止纵向堆叠**：不要把图表/表格放在单列文字下方，这会造成可读性差与布局问题

### 工作流
1. **必须 - 全文阅读**：完整阅读 [`html2pptx.md`](html2pptx.md)。**阅读时绝不要设置范围限制。**在开始创建演示文稿前先掌握详细语法、关键排版规则与最佳实践。
2. 为每张幻灯片创建一个 HTML 文件，并使用正确尺寸（例如 16:9 为 720pt × 405pt）
   - 所有文字内容只用 `<p>`、`<h1>`-`<h6>`、`<ul>`、`<ol>`
   - 图表/表格要插入的位置使用 `class="placeholder"`（用灰底渲染以便可视化定位）
   - **关键**：先用 Sharp 把渐变与图标栅格化为 PNG，再在 HTML 中引用
   - **布局**：含图表/表格/图片的幻灯片，使用全屏布局或两栏布局以提升可读性
3. 使用 [`html2pptx.js`](scripts/html2pptx.js) 编写并运行 JavaScript，把 HTML 幻灯片转换为 PowerPoint 并保存
   - 用 `html2pptx()` 处理每个 HTML 文件
   - 使用 PptxGenJS API 在 placeholder 区域添加图表与表格
   - 用 `pptx.writeFile()` 保存
4. **视觉校验**：生成缩略图并检查布局问题
   - 生成缩略图网格：`python scripts/thumbnail.py output.pptx workspace/thumbnails --cols 4`
   - 仔细检查缩略图中的问题：
     - **文字被裁切**：文字被顶部栏、形状或页面边缘裁掉
     - **文字重叠**：文字与文字/形状重叠
     - **定位问题**：内容离边界过近或与其他元素距离不合理
     - **对比度问题**：文字与背景对比不足
   - 如发现问题，调整 HTML 的边距/间距/颜色并重新生成
   - 直到所有幻灯片视觉正确为止

## 编辑现有 PowerPoint 演示文稿

编辑现有演示文稿时，需要直接处理 Office Open XML（OOXML）原始格式：解包 .pptx、编辑 XML、再打包回去。

### Workflow
1. **必须 - 全文阅读**：完整阅读 [`ooxml.md`](ooxml.md)（约 500 行）。**阅读时绝不要设置范围限制。**在开始编辑之前先掌握 OOXML 结构与编辑工作流。
2. 解包演示文稿：`python ooxml/scripts/unpack.py <office_file> <output_dir>`
3. 编辑 XML 文件（主要是 `ppt/slides/slide{N}.xml` 及相关文件）
4. **关键**：每次编辑后立刻校验并修复问题再继续：`python ooxml/scripts/validate.py <dir> --original <file>`
5. 打包回 .pptx：`python ooxml/scripts/pack.py <input_directory> <office_file>`

## **使用模板**新建 PowerPoint 演示文稿

当需要沿用现有模板的设计时，先复制/重排模板幻灯片，再替换占位内容。

### Workflow
1. **提取模板文字 + 生成视觉缩略图网格**：
   * Extract text: `python -m markitdown template.pptx > template-content.md`
   * Read `template-content.md`: 必须阅读全文以理解模板内容。**阅读时绝不要设置范围限制。**
   * Create thumbnail grids: `python scripts/thumbnail.py template.pptx`
   * See [Creating Thumbnail Grids](#creating-thumbnail-grids) section for more details

2. **分析模板并保存清单（inventory）到文件**：
   * **视觉分析**：通过缩略图网格理解版式、设计模式与视觉结构
   * 创建并保存 `template-inventory.md`，内容包含：
     ```markdown
     # Template Inventory Analysis
     **Total Slides: [count]**
     **IMPORTANT: Slides are 0-indexed (first slide = 0, last slide = count-1)**

     ## [Category Name]
     - Slide 0: [Layout code if available] - Description/purpose
     - Slide 1: [Layout code] - Description/purpose
     - Slide 2: [Layout code] - Description/purpose
     [... EVERY slide must be listed individually with its index ...]
     ```
   * **使用缩略图网格**：根据缩略图识别：
     - 版式模式（标题页、内容页布局、分节页等）
     - 图片占位符的位置与数量
     - 不同幻灯片组之间的设计一致性
     - 视觉层级与结构
   * 下一步选择模板时**必须**依赖此 inventory 文件

3. **基于模板 inventory 创建演示大纲**：
   * 回顾第 2 步可用的模板版式。
   * 为第一页选择引言/封面版式（通常选择模板靠前的版式之一）。
   * 为其余页面选择稳妥的纯文字版式。
   * **关键：版式结构必须匹配真实内容数量**：
     - 单列：用于统一叙事或单一主题
     - 双列：仅当你有且只有 2 个不同条目/概念时使用
     - 三列：仅当你有且只有 3 个不同条目/概念时使用
     - 图文：仅当确实有图片要插入时使用
     - 引用页：仅用于真实人物引用（带署名），不要用来做强调
     - 不要使用占位符数量多于内容数量的版式
     - 有 2 个条目时，不要硬塞进 3 列
     - 有 4+ 条目时，考虑拆成多页或改为列表版式
   * 选择版式之前先统计真实内容数量
   * 确保所选版式的每个占位符都会被有意义的内容填满
   * 每个内容段选择一种**最佳**版式
   * 保存 `outline.md`：包含内容与模板映射（利用现有设计）
   * 模板映射示例：
      ```
      # Template slides to use (0-based indexing)
      # WARNING: Verify indices are within range! Template with 73 slides has indices 0-72
      # Mapping: slide numbers from outline -> template slide indices
      template_mapping = [
          0,   # Use slide 0 (Title/Cover)
          34,  # Use slide 34 (B1: Title and body)
          34,  # Use slide 34 again (duplicate for second B1)
          50,  # Use slide 50 (E1: Quote)
          54,  # Use slide 54 (F2: Closing + Text)
      ]
      ```

4. **用 `rearrange.py` 复制/重排/删除幻灯片**：
   * 使用 `scripts/rearrange.py` 按所需顺序生成新演示文稿：
     ```bash
     python scripts/rearrange.py template.pptx working.pptx 0,34,34,50,52
     ```
   * 脚本会自动处理：复制重复幻灯片、删除未使用幻灯片、重排顺序
   * 幻灯片索引从 0 开始（第 1 张为 0、第 2 张为 1，以此类推）
   * 同一个索引可出现多次，以实现复制该幻灯片

5. **用 `inventory.py` 提取全部文字**：
   * **运行 inventory 提取**：
     ```bash
     python scripts/inventory.py working.pptx text-inventory.json
     ```
   * **Read text-inventory.json**：必须阅读全文以理解所有 shape 及其属性。**阅读时绝不要设置范围限制。**

   * inventory 的 JSON 结构：
      ```json
        {
          "slide-0": {
            "shape-0": {
              "placeholder_type": "TITLE",  // or null for non-placeholders
              "left": 1.5,                  // position in inches
              "top": 2.0,
              "width": 7.5,
              "height": 1.2,
              "paragraphs": [
                {
                  "text": "Paragraph text",
                  // Optional properties (only included when non-default):
                  "bullet": true,           // explicit bullet detected
                  "level": 0,               // only included when bullet is true
                  "alignment": "CENTER",    // CENTER, RIGHT (not LEFT)
                  "space_before": 10.0,     // space before paragraph in points
                  "space_after": 6.0,       // space after paragraph in points
                  "line_spacing": 22.4,     // line spacing in points
                  "font_name": "Arial",     // from first run
                  "font_size": 14.0,        // in points
                  "bold": true,
                  "italic": false,
                  "underline": false,
                  "color": "FF0000"         // RGB color
                }
              ]
            }
          }
        }
      ```

   * 关键特性：
     - **Slides**: Named as "slide-0", "slide-1", etc.
     - **Shapes**: Ordered by visual position (top-to-bottom, left-to-right) as "shape-0", "shape-1", etc.
     - **Placeholder types**: TITLE, CENTER_TITLE, SUBTITLE, BODY, OBJECT, or null
     - **Default font size**: `default_font_size` in points extracted from layout placeholders (when available)
     - **Slide numbers are filtered**: Shapes with SLIDE_NUMBER placeholder type are automatically excluded from inventory
     - **项目符号**：当 `bullet: true` 时，`level` 总会包含（即使是 0）
     - **Spacing**: `space_before`, `space_after`, and `line_spacing` in points (only included when set)
     - **Colors**: `color` for RGB (e.g., "FF0000"), `theme_color` for theme colors (e.g., "DARK_1")
     - **Properties**: Only non-default values are included in the output

6. **生成替换文本并保存为 JSON**
   基于上一步的文本 inventory：
   - **关键**：先确认 inventory 里有哪些 shape，只能引用真实存在的 shape
   - **校验**：replace.py 会验证你的替换 JSON 中引用的所有 shape 是否存在于 inventory
     - 引用不存在的 shape 会报错，并提示可用 shape 列表
     - 引用不存在的 slide 会报错并提示不存在
     - 所有校验错误会在脚本退出前一次性输出
   - **重要**：replace.py 内部会调用 inventory.py 识别所有文字 shape
   - **自动清空**：除非你为某个 shape 提供 "paragraphs"，否则 inventory 中所有文字 shape 都会被清空
   - 需要内容的 shape 添加 "paragraphs" 字段（不是 "replacement_paragraphs"）
   - 替换 JSON 中未包含 "paragraphs" 的 shape 会被自动清空
   - 带 bullet 的段落会自动左对齐。当 `"bullet": true` 时不要再设置 `alignment`
   - 为占位文本生成合适的替换内容
   - 结合 shape 尺寸决定内容长度
   - **关键**：要包含原 inventory 的段落属性，不要只提供纯文本
   - **重要**：当 bullet: true 时，text 里不要包含项目符号（•、-、*），它们会自动添加
   - **必要格式规则**：
     - 标题/页眉通常应 `"bold": true`
     - 列表项需要 `"bullet": true, "level": 0`（bullet 为 true 时 level 必须存在）
     - 保留对齐属性（例如居中用 `"alignment": "CENTER"`）
     - 若字体属性与默认不同则提供（例如 `"font_size": 14.0`、`"font_name": "Lora"`）
     - 颜色：RGB 用 `"color": "FF0000"`；主题色用 `"theme_color": "DARK_1"`
     - 替换脚本期望的是**格式完整的段落对象**，而不是纯字符串
     - **重叠 shape**：优先选择 default_font_size 更大或 placeholder_type 更合适的 shape
   - 将带替换内容的 inventory 保存为 `replacement-text.json`
   - **警告**：不同模板版式 shape 数量不同，生成替换前务必以实际 inventory 为准

   Example paragraphs field showing proper formatting:
   ```json
   "paragraphs": [
     {
       "text": "New presentation title text",
       "alignment": "CENTER",
       "bold": true
     },
     {
       "text": "Section Header",
       "bold": true
     },
     {
       "text": "First bullet point without bullet symbol",
       "bullet": true,
       "level": 0
     },
     {
       "text": "Red colored text",
       "color": "FF0000"
     },
     {
       "text": "Theme colored text",
       "theme_color": "DARK_1"
     },
     {
       "text": "Regular paragraph text without special formatting"
     }
   ]
   ```

   **替换 JSON 未列出的 shape 会被自动清空**：
   ```json
   {
     "slide-0": {
       "shape-0": {
         "paragraphs": [...] // This shape gets new text
       }
       // shape-1 and shape-2 from inventory will be cleared automatically
     }
   }
   ```

   **演示文稿常见格式模式**：
   - 标题页：加粗文字，部分情况下居中
   - 页内小节标题：加粗
   - 项目符号列表：每一项都需要 `"bullet": true, "level": 0`
   - 正文：通常不需要特殊属性
   - 引用：可能需要特殊对齐或字体属性

7. **使用 `replace.py` 应用替换**
   ```bash
   python scripts/replace.py working.pptx replacement-text.json output.pptx
   ```

   脚本会：
   - First extract the inventory of ALL text shapes using functions from inventory.py
   - Validate that all shapes in the replacement JSON exist in the inventory
   - Clear text from ALL shapes identified in the inventory
   - Apply new text only to shapes with "paragraphs" defined in the replacement JSON
   - Preserve formatting by applying paragraph properties from the JSON
   - Handle bullets, alignment, font properties, and colors automatically
   - Save the updated presentation

   校验错误示例：
   ```
   ERROR: Invalid shapes in replacement JSON:
     - Shape 'shape-99' not found on 'slide-0'. Available shapes: shape-0, shape-1, shape-4
     - Slide 'slide-999' not found in inventory
   ```

   ```
   ERROR: Replacement text made overflow worse in these shapes:
     - slide-0/shape-2: overflow worsened by 1.25" (was 0.00", now 1.25")
   ```

## 生成缩略图网格（Thumbnail Grids）

为快速分析与引用，生成 PowerPoint 幻灯片的缩略图网格：

```bash
python scripts/thumbnail.py template.pptx [output_prefix]
```

**特性**：
- 生成：`thumbnails.jpg`（或 `thumbnails-1.jpg`、`thumbnails-2.jpg` 等，用于超大演示文稿）
- 默认：5 列；每张网格最多 30 页（5×6）
- 自定义前缀：`python scripts/thumbnail.py template.pptx my-grid`
  - 注意：若希望输出到特定目录，前缀应包含路径（例如 `workspace/my-grid`）
- 调整列数：`--cols 4`（范围 3–6，会影响每张网格的页数）
- 网格容量：3 列 = 12 页/张，4 列 = 20，5 列 = 30，6 列 = 42
- 幻灯片编号从 0 开始（Slide 0、Slide 1 等）

**使用场景**：
- 模板分析：快速理解版式与设计模式
- 内容审阅：对整份演示做视觉总览
- 导航定位：按视觉外观快速找到特定页面
- 质量检查：确认所有幻灯片排版正确

**示例**：
```bash
# Basic usage
python scripts/thumbnail.py presentation.pptx

# Combine options: custom name, columns
python scripts/thumbnail.py template.pptx analysis --cols 4
```

## 将幻灯片转换为图片

要可视化分析 PowerPoint 幻灯片，可按两步转换为图片：

1. **PPTX 转 PDF**：
   ```bash
   soffice --headless --convert-to pdf template.pptx
   ```

2. **PDF 页面转 JPEG 图片**：
   ```bash
   pdftoppm -jpeg -r 150 template.pdf slide
   ```
   会生成类似 `slide-1.jpg`、`slide-2.jpg` 的文件。

可选参数：
- `-r 150`：分辨率设为 150 DPI（按质量/体积平衡调整）
- `-jpeg`：输出 JPEG（如需 PNG 则用 `-png`）
- `-f N`：起始页（例如 `-f 2` 从第 2 页开始）
- `-l N`：结束页（例如 `-l 5` 在第 5 页结束）
- `slide`：输出文件前缀

指定范围示例：
```bash
pdftoppm -jpeg -r 150 -f 2 -l 5 template.pdf slide  # Converts only pages 2-5
```

## 代码风格指南
**重要**：生成 PPTX 相关代码时：
- 代码要简洁
- 避免冗长变量名与重复操作
- 避免不必要的 print 输出

## 依赖

所需依赖（通常已安装）：

- **markitdown**: `pip install "markitdown[pptx]"` (for text extraction from presentations)
- **pptxgenjs**: `npm install -g pptxgenjs` (for creating presentations via html2pptx)
- **playwright**: `npm install -g playwright` (for HTML rendering in html2pptx)
- **react-icons**: `npm install -g react-icons react react-dom` (for icons)
- **sharp**: `npm install -g sharp` (for SVG rasterization and image processing)
- **LibreOffice**: `sudo apt-get install libreoffice` (for PDF conversion)
- **Poppler**: `sudo apt-get install poppler-utils` (for pdftoppm to convert PDF to images)
- **defusedxml**: `pip install defusedxml` (for secure XML parsing)
