---
name: algorithmic-art
description: 使用 p5.js 创作算法艺术：包含可复现的种子随机（seeded randomness）与交互式参数探索。用户提出用代码创作艺术、生成艺术、算法艺术、流场或粒子系统等需求时使用。必须创作原创算法艺术，避免复制既有艺术家作品以规避版权风险。
license: Complete terms in LICENSE.txt
---

“算法哲学”是一种计算审美运动，随后通过代码被表达出来。输出 .md（哲学）、.html（交互式查看器）与 .js（生成算法）。

按两步完成：
1. 算法哲学创作（.md 文件）
2. 用 p5.js 生成艺术进行表达（.html + .js 文件）

首先完成以下任务：

## 算法哲学创作

先创建一个“算法哲学”（不是静态图片或模板），它将通过以下维度被解读：
- 计算过程、涌现行为、数学之美
- 可复现的种子随机、噪声场、有机系统
- 粒子、流动、场、力
- 参数化变化与可控混沌

### 关键理解
- 收到的是什么：用户的一些微妙输入/指令，应作为基础被纳入考虑，但不应限制创作自由。
- 创造的是什么：一种算法哲学/生成审美运动。
- 接下来发生什么：同一个版本拿到哲学后将其“写进代码”——生成的 p5.js sketch 应 90% 来自算法生成，10% 来自必要参数。

可采用如下方式：
- 为一种生成艺术运动写一份宣言
- 下一阶段编写算法，让其真正“活起来”

哲学必须强调：算法表达、涌现行为、计算之美、种子化的可控变化。

### 如何生成算法哲学

**为运动命名**（1–2 个词）：例如 "Organic Turbulence" / "Quantum Harmonics" / "Emergent Stillness"

**阐述哲学**（4–6 段，精炼但完整）：

为捕捉“算法本质”，说明哲学如何通过以下方面体现：
- 计算过程与数学关系？
- 噪声函数与随机模式？
- 粒子行为与场动力学？
- 时间演化与系统状态？
- 参数化变化与涌现复杂度？

**关键准则：**
- **避免重复**：每个算法要点只提一次。除非增加新的深度，否则不要重复噪声理论、粒子动力学或数学原理。
- **反复强调匠心与打磨**：哲学必须多次强调最终算法应看起来像花费了无数小时开发与精修，出自该领域顶尖人士之手。这种框架很关键——反复使用类似 “meticulously crafted algorithm”“the product of deep computational expertise”“painstaking optimization”“master-level implementation” 的措辞。
- **保留创作空间**：对算法方向要具体，但又要足够精炼，让下一位 Claude 仍能在极高工艺水准下做出诠释性实现选择。

哲学必须引导下一阶段用“算法”而非“静态图像”来表达。美存在于过程，而不是最终帧。

### 哲学示例

**"Organic Turbulence"**
哲学：混沌被自然法则约束，秩序从失序中涌现。
算法表达：由多层 Perlin 噪声驱动的流场。成千上万粒子沿向量力场运动，轨迹叠加成有机的密度地图。多倍频噪声构造湍动区域与宁静区域。颜色从速度与密度中生成——快粒子燃亮，慢粒子隐入阴影。算法运行至系统达成平衡：每个参数都经过生成审美大师的无数次迭代精修，形成一套被精密调校的均衡。

**"Quantum Harmonics"**
哲学：离散实体呈现波动式干涉图样。
算法表达：粒子在网格上初始化，每个粒子携带随正弦波演化的相位值。粒子相近时相位发生干涉——相长干涉形成明亮结点，相消干涉形成空洞。简单谐振运动生成复杂的涌现曼荼罗。每个频率比值都被精心校准，只为产生共振之美。

**"Recursive Whispers"**
哲学：跨尺度的自相似，有限空间中的无限深度。
算法表达：递归细分的分枝结构。每一枝条带有轻微随机，但被黄金比例约束。用 L-system 或递归细分生成既数学又有机的树状形态。细微噪声扰动打破绝对对称；线条随递归层级逐渐变细。每个分枝角度都是深入数学探索后的产物。

**"Field Dynamics"**
哲学：看不见的力，通过其对物质的作用而显形。
算法表达：用数学函数或噪声构造向量场。粒子从边缘诞生，沿场线流动，到达边界或平衡时消亡。多重场可对粒子产生吸引、排斥或旋转。可视化只呈现轨迹——像幽灵般的证据，证明无形之力的存在。一支由力的平衡被精密编排的计算之舞。

**"Stochastic Crystallization"**
哲学：随机过程结晶为有序结构。
算法表达：随机圆填充或 Voronoi 剖分。以随机点为起点，经由松弛算法演化；单元彼此推开直至平衡。颜色可由单元尺寸、邻居数量或到中心的距离决定。涌现出的有机铺砖既随机又必然。每个 seed 都生成独一无二的晶体之美——这是大师级生成算法的印记。

*以上为压缩示例。实际的算法哲学应是 4–6 段有分量的文字。*

### 核心原则
- **算法哲学**：创建一种通过代码表达的计算世界观
- **过程优先于结果**：反复强调美来自算法的执行过程——每次运行都独一无二
- **参数化表达**：理念通过数学关系、力与行为来沟通，而不是静态构图
- **艺术自由**：下一位 Claude 以算法方式诠释哲学——保留创作实现空间
- **纯生成艺术**：这是在做“活的算法”，不是给静态图像加随机
- **专家级匠心**：反复强调最终算法必须显得被精心制作、经历无数迭代精修，出自计算审美领域顶尖专家的深厚功力

**算法哲学应为 4–6 段。**用具有诗性但可执行的计算哲学把愿景聚合起来，避免重复。输出为 .md 文件。

---

## 推导概念种子（Conceptual Seed）

**关键步骤**：在开始实现算法前，从原始需求中识别一条微妙的概念线索。

**核心原则：**
概念应是**嵌入算法本身的微妙小众引用**——不一定字面化，但必须精致。了解该主题的人会直觉感受到它；不了解的人也能单纯感受到一件高水平生成构图。算法哲学提供计算语言；推导出的概念提供灵魂——把静默的概念 DNA 无形地织入参数、行为与涌现模式。

这点**非常重要**：引用必须足够克制与精炼，增强作品深度但不自我宣告。像爵士乐手用算法和声暗引另一首歌——懂的人会捕捉到，不懂的人也照样欣赏生成之美。

---

## P5.JS 实现

在哲学与概念框架确立后，用代码把它表达出来。开始前先停一下整理思路。只使用已创建的算法哲学以及以下指令。

### ⚠️ 第 0 步：先读模板 ⚠️

**关键：在写任何 HTML 之前：**

1. 使用 Read 工具 **Read** `templates/viewer.html`
2. **Study** 其精确结构、样式与 Anthropic 品牌规范
3. **把该文件作为“字面意义上的起点”**——不是灵感参考
4. **所有 FIXED 区域必须原样保留**（header、sidebar 结构、Anthropic 颜色/字体、seed 控件、action 按钮）
5. **只替换 VARIABLE 区域**（文件注释中标记的算法、参数、参数 UI 控件）

**避免：**
- ❌ Creating HTML from scratch
- ❌ Inventing custom styling or color schemes
- ❌ Using system fonts or dark themes
- ❌ Changing the sidebar structure

**遵循：**
- ✅ Copy the template's exact HTML structure
- ✅ Keep Anthropic branding (Poppins/Lora fonts, light colors, gradient backdrop)
- ✅ Maintain the sidebar layout (Seed → Parameters → Colors? → Actions)
- ✅ Replace only the p5.js algorithm and parameter controls

模板是地基。在其上建设，不要重建。

---

要创作具有画廊品质、能“呼吸”的计算艺术，以算法哲学为基础。

### 技术要求

**Seeded Randomness (Art Blocks Pattern)**:
```javascript
// ALWAYS use a seed for reproducibility
let seed = 12345; // or hash from user input
randomSeed(seed);
noiseSeed(seed);
```

**参数结构——遵循哲学**：

要让参数从算法哲学中自然“长出来”，可问：“这个系统哪些性质需要可调？”

```javascript
let params = {
  seed: 12345,  // Always include seed for reproducibility
  // colors
  // Add parameters that control YOUR algorithm:
  // - Quantities (how many?)
  // - Scales (how big? how fast?)
  // - Probabilities (how likely?)
  // - Ratios (what proportions?)
  // - Angles (what direction?)
  // - Thresholds (when does behavior change?)
};
```

**设计有效参数时，关注系统需要可调的性质，而不是把思路限定在“图案类型”上。**

**核心算法——表达哲学**：

**关键**：要做什么，应由算法哲学决定。

用代码表达哲学时，不要想“我该用哪种 pattern？”，而要想“如何用代码表达这套哲学？”

如果哲学强调**有机涌现**，可考虑：
- Elements that accumulate or grow over time
- Random processes constrained by natural rules
- Feedback loops and interactions

如果哲学强调**数学之美**，可考虑：
- Geometric relationships and ratios
- Trigonometric functions and harmonics
- Precise calculations creating unexpected patterns

如果哲学强调**可控混沌**，可考虑：
- Random variation within strict boundaries
- Bifurcation and phase transitions
- Order emerging from disorder

**算法应从哲学流出，而不是从选项菜单里挑出来。**

实现时让概念本质驱动创意与原创选择，构建一个专为本次需求而生的表达方式。

**画布初始化**：标准 p5.js 结构：
```javascript
function setup() {
  createCanvas(1200, 1200);
  // Initialize your system
}

function draw() {
  // Your generative algorithm
  // Can be static (noLoop) or animated
}
```

### 工艺要求（Craftsmanship）

**关键**：要达到大师级水准，算法必须让人感觉像出自生成艺术大师无数次迭代精修。认真调每个参数，确保每个图案的出现都有目的。这不是随机噪声——这是由深厚经验精炼出的可控混沌。

- **Balance**: Complexity without visual noise, order without rigidity
- **Color Harmony**: Thoughtful palettes, not random RGB values
- **Composition**: Even in randomness, maintain visual hierarchy and flow
- **Performance**: Smooth execution, optimized for real-time if animated
- **Reproducibility**: Same seed ALWAYS produces identical output

### 输出格式

输出：
1. **算法哲学** - 用 markdown 或文本解释生成审美
2. **单个 HTML 产物** - 从 `templates/viewer.html` 构建的自包含交互生成艺术（见第 0 步与下一节）

HTML 产物必须包含一切：p5.js（CDN）、算法、参数控件与 UI——全部在一个文件里，可在 claude.ai artifacts 或任意浏览器中立即运行。必须从模板开始，不要从零写起。

---

## 交互式产物创建

**提醒：应已阅读 `templates/viewer.html`（见第 0 步）。以该文件为起点。**

为便于探索生成艺术，创建一个单文件、自包含的 HTML 产物。该产物应在 claude.ai 或任意浏览器中无需配置即可运行，所有内容都内联。

### 关键：固定项 vs 可变项

`templates/viewer.html` 是基础模板，包含必须遵循的结构与样式。

**固定项（必须原样包含）：**
- 布局结构（header、sidebar、主画布区域）
- Anthropic 品牌风格（UI 颜色、字体、渐变等）
- 侧边栏 Seed 区块：
  - Seed 显示
  - Previous/Next 按钮
  - Random 按钮
  - Jump to seed 输入框 + Go 按钮
- 侧边栏 Actions 区块：
  - Regenerate 按钮
  - Reset 按钮

**可变项（每个作品都应定制）：**
- 完整的 p5.js 算法（setup/draw/类等）
- 参数对象（定义作品需要哪些参数）
- 侧边栏中的 Parameters 区块：
  - 参数控件数量
  - 参数名称
  - slider 的 min/max/step
  - 控件类型（slider、输入框等）
- Colors 区块（可选）：
  - 部分作品需要颜色选择器
  - 部分作品使用固定配色
  - 部分作品可能是单色（无需颜色控件）
  - 是否需要取决于作品需求

**每个作品都必须有独特的参数与算法！**固定部分提供一致的使用体验，其余部分用于表达本次作品的独特愿景。

### 必备功能

**1. 参数控件（Parameter Controls）**
- 数值参数使用 slider（粒子数量、噪声尺度、速度等）
- 调色盘颜色使用颜色选择器
- 参数变更后实时更新画面
- Reset 按钮恢复默认值

**2. Seed 导航（Seed Navigation）**
- 显示当前 seed 数值
- Previous / Next 按钮切换 seed
- Random 按钮生成随机 seed
- 输入框跳转到指定 seed
- 用户要求时生成 100 个变体（seed 1–100）

**3. 单文件产物结构（Single Artifact Structure）**
```html
<!DOCTYPE html>
<html>
<head>
  <!-- p5.js from CDN - always available -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.7.0/p5.min.js"></script>
  <style>
    /* All styling inline - clean, minimal */
    /* Canvas on top, controls below */
  </style>
</head>
<body>
  <div id="canvas-container"></div>
  <div id="controls">
    <!-- All parameter controls -->
  </div>
  <script>
    // ALL p5.js code inline here
    // Parameter objects, classes, functions
    // setup() and draw()
    // UI handlers
    // Everything self-contained
  </script>
</body>
</html>
```

**关键**：这是单文件产物。除 p5.js CDN 外不依赖外部文件/导入；所有内容必须内联。

**4. 实现细节：构建侧边栏（BUILD THE SIDEBAR）**

侧边栏结构：

**1. Seed（固定 FIXED）** - 必须完全按模板保留：
- Seed 显示
- Prev/Next/Random/Jump 按钮

**2. Parameters（可变 VARIABLE）** - 为作品创建参数控件：
```html
<div class="control-group">
    <label>Parameter Name</label>
    <input type="range" id="param" min="..." max="..." step="..." value="..." oninput="updateParam('param', this.value)">
    <span class="value-display" id="param-value">...</span>
</div>
```
根据参数数量添加对应数量的 control-group。

**3. Colors（可选 OPTIONAL/可变 VARIABLE）** - 只有在作品需要可调色彩时才加入：
- 若需要用户调整配色，则添加颜色选择器
- 若作品使用固定配色，则跳过该区块
- 若作品为单色，也可跳过该区块

**4. Actions（固定 FIXED）** - 必须完全按模板保留：
- Regenerate 按钮
- Reset 按钮
- Download PNG 按钮

**要求**：
- Seed 控件必须可用（prev/next/random/jump/display）
- 所有参数都必须有对应 UI 控件
- Regenerate / Reset / Download 按钮必须可用
- 保持 Anthropic 品牌风格（UI 样式，而非作品配色）

### 使用方式

HTML 产物应可立即运行：
1. **在 claude.ai**：以交互式 artifact 展示，立即运行
2. **作为文件**：保存后用任意浏览器打开，无需服务器
3. **分享**：直接发送 HTML 文件即可，完全自包含

---

## 变体与探索

产物默认包含 seed 导航（prev/next/random 按钮），用户无需生成多个文件即可探索变体。若用户希望突出某些变体：

- 提供 seed 预设按钮（例如“Variation 1: Seed 42”“Variation 2: Seed 127”等）
- 增加“Gallery Mode”，并排展示多个 seed 的缩略图
- 所有内容仍然保持在同一个单文件产物中

这像是从同一块版材印制一系列版画：算法保持一致，但不同 seed 会揭示不同侧面。交互性让用户通过探索 seed 空间发现自己的偏好。

---

## 创作流程

**用户需求** → **算法哲学** → **实现**

每个需求都是独特的，流程包括：

1. **理解用户意图**：想要的审美与氛围是什么？
2. **创建算法哲学**（4–6 段）：描述计算方法与审美方向
3. **用代码实现**：写出表达该哲学的算法
4. **设计合适参数**：哪些性质需要可调？
5. **构建匹配的 UI 控件**：为这些参数提供 slider/输入框等

**恒定不变的部分：**
- Anthropic 品牌风格（颜色、字体、布局）
- Seed 导航（始终存在）
- 单文件、自包含的 HTML 产物

**其余一切都是可变项：**
- 算法本身
- 参数设计
- UI 控件
- 最终视觉效果

要获得最佳结果，相信创造力，并让哲学引导实现。

---

## 资源

本技能包含以下模板与文档资源：

- **templates/viewer.html**：所有 HTML 产物的必选起点。
  - 这是地基：包含精确结构与 Anthropic 品牌规范
  - **保持不变**：布局结构、侧边栏组织、Anthropic 颜色/字体、seed 控件、action 按钮
  - **需要替换**：p5.js 算法、参数定义、Parameters 区域的 UI 控件
  - 文件内大量注释会标明哪些要保留、哪些要替换

- **templates/generator_template.js**：p5.js 最佳实践与代码结构原则参考。
  - 展示如何组织参数、使用种子随机、组织类结构
  - 不是 pattern 菜单——用这些原则构建独特算法
  - 在 HTML 产物中内联算法（不要创建独立 .js 文件）

**关键提醒：**
- **模板是起点**，不是灵感
- **独特性在算法里产生**
- 不要照抄 flow field 示例——要构建哲学所要求的东西
- 但必须保留模板中完全一致的 UI 结构与 Anthropic 品牌规范
