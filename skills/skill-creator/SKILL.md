---
name: skill-creator
description: 用于创建高质量技能（skills）的指南。当用户想创建新技能（或更新已有技能），以通过专门知识、工作流或工具集成扩展 Claude 能力时使用。
license: Complete terms in LICENSE.txt
---

# 技能创建器（Skill Creator）

此技能提供创建高质量技能的指导。

## 关于技能（Skills）

Skills 是模块化、可自包含的能力包，通过提供专门知识、工作流与工具来扩展 Claude。可以把它们理解为特定领域/任务的“入职手册”：把通用 Agent 变成具备流程化知识的专用 Agent——这些程序化知识并非任何模型都能天然完全具备。

### Skills 提供什么

1. 专门工作流：针对特定领域的多步骤流程
2. 工具集成：处理特定文件格式或 API 的使用说明
3. 领域知识：公司特有知识、schema、业务逻辑
4. 随附资源：用于复杂/重复任务的脚本、参考文档与资源文件

## 核心原则

### 简洁第一

上下文窗口是一种“公共资源”。Skills 会和 Claude 所需的其他内容共享上下文窗口：系统提示、对话历史、其他技能的元数据，以及用户的真实需求。

**默认假设：Claude 已经很聪明。**只添加 Claude 原本不知道的上下文。对每一段内容都要自我质疑：“Claude 真的需要这段解释吗？”“这段文字的 token 成本值得吗？”

宁可用简短例子，也不要写冗长解释。

### 设置合适的自由度

具体程度要匹配任务的脆弱性与可变性：

**高自由度（纯文本指导）**：适用于多种方法都可行、决策高度依赖上下文、或主要依靠经验法则的场景。

**中自由度（伪代码或可配置脚本）**：适用于存在推荐模式、允许一定变化、或行为受配置影响的场景。

**低自由度（固定脚本、少量参数）**：适用于操作脆弱易错、一致性至关重要、或必须遵循固定顺序的场景。

把 Claude 想象成在探索一条路：两侧是悬崖的窄桥需要明确护栏（低自由度），而开阔的草地可以允许多条路线（高自由度）。

### 技能的组成（Anatomy）

每个技能由必需的 SKILL.md 与可选的随附资源组成：

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required)
│   │   └── description: (required)
│   └── Markdown instructions (required)
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation intended to be loaded into context as needed
    └── assets/           - Files used in output (templates, icons, fonts, etc.)
```

#### SKILL.md (required)

Every SKILL.md consists of:

- **Frontmatter**（YAML）：包含 `name` 与 `description`。这是 Claude 用来判断何时触发该技能的唯一信息，因此必须清晰且全面地描述“技能做什么/何时用”。
- **Body**（Markdown）：使用技能的说明与指导。仅在技能触发后（如果需要）才会被加载。

#### Bundled Resources (optional)

##### Scripts (`scripts/`)

Executable code (Python/Bash/etc.) for tasks that require deterministic reliability or are repeatedly rewritten.

- **何时加入**：同一段代码反复被重写，或需要确定性可靠性时
- **Example**: `scripts/rotate_pdf.py` for PDF rotation tasks
- **收益**：节省上下文 token、确定性强，且可在不读入上下文的情况下直接执行
- **注意**：脚本有时仍需被 Claude 阅读以便打补丁或做环境适配

##### References (`references/`)

Documentation and reference material intended to be loaded as needed into context to inform Claude's process and thinking.

- **何时加入**：需要 Claude 在工作时随时查阅的文档
- **示例**：`references/finance.md`（财务 schema）、`references/mnda.md`（公司 NDA 模板）、`references/policies.md`（公司政策）、`references/api_docs.md`（API 规范）
- **适用场景**：数据库 schema、API 文档、领域知识、公司政策、详细工作流指南
- **收益**：让 SKILL.md 保持精简，仅在 Claude 判断需要时加载
- **最佳实践**：若文件很大（>10k 字），在 SKILL.md 中给出 grep 搜索模式
- **避免重复**：信息要么放在 SKILL.md，要么放在 references，不要两边都写。除非确实是技能的核心内容，否则优先把细节放 references，从而保持 SKILL.md 精简、同时又可发现，不挤占上下文。

##### Assets (`assets/`)

Files not intended to be loaded into context, but rather used within the output Claude produces.

- **何时加入**：技能需要在最终产物中使用到文件时
- **示例**：`assets/logo.png`（品牌素材）、`assets/slides.pptx`（PPT 模板）、`assets/frontend-template/`（HTML/React 脚手架）、`assets/font.ttf`（字体）
- **适用场景**：模板、图片、图标、样板代码、字体、会被复制或修改的示例文档
- **收益**：把产物资源与文档分离，使 Claude 可使用文件但不必读入上下文

#### What to Not Include in a Skill

A skill should only contain essential files that directly support its functionality. Do NOT create extraneous documentation or auxiliary files, including:

- README.md
- INSTALLATION_GUIDE.md
- QUICK_REFERENCE.md
- CHANGELOG.md
- etc.

技能只应包含 AI agent 完成任务所需的信息。不要加入创建过程的闲聊背景、安装/测试流程、面向用户的文档等。额外文档只会增加混乱与维护成本。

### 渐进式披露（Progressive Disclosure）原则

Skills 用三层加载体系高效管理上下文：

1. **元数据（name + description）**：始终在上下文中（约 100 词）
2. **SKILL.md 正文**：技能触发后加载（< 5k 词）
3. **打包资源（Bundled resources）**：按需加载（理论无限，因为脚本可执行而无需读入上下文）

#### 渐进式披露模式

SKILL.md 的正文只保留必要内容，并尽量控制在 500 行以内以减少上下文膨胀。接近上限时把内容拆分到其他文件。拆分后务必在 SKILL.md 中引用，并清晰说明何时阅读，以确保使用者知道它们存在并知道何时加载。

**关键原则：**当技能支持多种变体/框架/选项时，SKILL.md 里只保留核心工作流与选择指导；变体细节（模式、示例、配置）移到独立 references 文件中。

**Pattern 1: High-level guide with references**

```markdown
# PDF Processing

## Quick start

Extract text with pdfplumber:
[code example]

## Advanced features

- **Form filling**: See [FORMS.md](FORMS.md) for complete guide
- **API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
- **Examples**: See [EXAMPLES.md](EXAMPLES.md) for common patterns
```

Claude 仅在需要时才加载 FORMS.md、REFERENCE.md 或 EXAMPLES.md。

**Pattern 2: Domain-specific organization**

当技能涵盖多个业务域时，按域组织内容以避免加载无关上下文：

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

当用户问销售指标时，Claude 只读取 sales.md。

同样地，支持多框架/多变体的技能可按变体组织：

```
cloud-deploy/
├── SKILL.md (workflow + provider selection)
└── references/
    ├── aws.md (AWS deployment patterns)
    ├── gcp.md (GCP deployment patterns)
    └── azure.md (Azure deployment patterns)
```

当用户选择 AWS 时，Claude 只读取 aws.md。

**Pattern 3: Conditional details**

先展示基础内容，并链接到高级内容：

```markdown
# DOCX Processing

## Creating documents

Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).

## Editing documents

For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

Claude 仅在用户需要相关能力时才读取 REDLINING.md 或 OOXML.md。

**重要指南：**

- **避免多层嵌套引用**：references 深度保持在 SKILL.md 的下一层，所有 reference 文件都应从 SKILL.md 直接链接
- **组织长 reference 文件**：超过 100 行的文件在开头放目录，便于预览时快速了解全貌

## 技能创建流程

创建技能通常包含以下步骤：

1. 用具体例子理解技能需求
2. 规划可复用的技能内容（scripts/references/assets）
3. 初始化技能（运行 init_skill.py）
4. 编辑技能（实现资源并编写 SKILL.md）
5. 打包技能（运行 package_skill.py）
6. 基于真实使用迭代

按顺序执行这些步骤，只有在明确不适用时才跳过。

### 步骤 1：用具体例子理解技能

仅当技能的使用模式已经非常清楚时才跳过该步骤。即便在迭代已有技能时，这一步也很有价值。

要创建有效的技能，必须先清晰理解它会被如何使用的具体例子。理解来源可以是用户直接提供的例子，也可以是你生成后再经用户反馈验证的例子。

例如，在构建 image-editor 技能时，相关问题可能包括：

- “image-editor 技能要支持哪些能力？编辑、旋转，还有别的吗？”
- “你能给一些这个技能会被如何使用的例子吗？”
- “我能想到用户会说‘去掉红眼’或‘旋转图片’之类需求。你还希望有哪些触发方式/用法？”
- “用户会说什么话，才应该触发这个技能？”

为避免压垮用户，不要在一条消息里问太多问题。先问最关键的问题，再按需追问。

当你已清晰理解该技能应支持的功能边界时结束本步骤。

### 步骤 2：规划可复用的技能内容

要把具体例子变成可用技能，需要逐个分析每个例子：

1. 思考如果从零开始执行这个例子，应如何做
2. 识别在重复执行这些工作流时，哪些 scripts/references/assets 会有帮助

示例：构建 `pdf-editor` 技能以处理 “Help me rotate this PDF” 之类需求时，可分析出：

1. 旋转 PDF 往往需要每次重写相同代码
2. 可以把 `scripts/rotate_pdf.py` 脚本沉淀到技能里复用

示例：为 “Build me a todo app” 或 “Build me a dashboard to track my steps” 之类需求设计 `frontend-webapp-builder` 技能时，可分析出：

1. 开发前端 WebApp 往往需要每次重复同样的 HTML/React 样板工程
2. 可以把包含样板项目文件的 `assets/hello-world/` 模板沉淀到技能里复用

示例：构建 `big-query` 技能以处理 “How many users have logged in today?” 之类需求时，可分析出：

1. 查询 BigQuery 往往需要每次重新摸清表结构与关系
2. 可以把记录表结构的 `references/schema.md` 沉淀到技能里复用

最终基于这些具体例子，整理出需要纳入技能的可复用资源清单：scripts、references、assets。

### 步骤 3：初始化技能

此时应开始实际创建技能。

仅当该技能已经存在且你只是要迭代或打包时，才跳过此步骤；跳过后直接进入下一步。

从零创建新技能时，务必运行 `init_skill.py`。该脚本会生成一个包含技能所需结构的模板目录，使创建过程更高效且更可靠。

用法：

```bash
scripts/init_skill.py <skill-name> --path <output-directory>
```

脚本会：

- 在指定路径创建技能目录
- 生成带正确 frontmatter 与 TODO 占位符的 SKILL.md 模板
- 创建示例资源目录：`scripts/`、`references/`、`assets/`
- 在每个目录中添加可自定义或删除的示例文件

初始化后，按需修改或删除生成的 SKILL.md 与示例文件。

### 步骤 4：编辑技能

编辑（新生成或已有）技能时要记住：这是给“另一个 Claude 实例”使用的。只加入对 Claude 有帮助且不显而易见的信息。思考哪些流程性知识、领域细节或可复用资源能让另一个 Claude 更高效地完成任务。

#### 学习成熟的设计模式

根据技能需求参考以下指南：

- **多步骤流程**：参考 references/workflows.md（顺序工作流与条件逻辑）
- **特定输出格式/质量标准**：参考 references/output-patterns.md（模板与示例模式）

这些文件包含成熟的技能设计最佳实践。

#### Start with Reusable Skill Contents

开始实现时，先从上一步识别出的可复用资源入手：`scripts/`、`references/`、`assets/`。注意该步骤可能需要用户提供输入。例如实现 `brand-guidelines` 技能时，用户可能需要提供要放进 `assets/` 的品牌素材/模板，或要放进 `references/` 的文档。

新增脚本必须通过实际运行来测试，确保无 bug 且输出符合预期。若脚本很多且相似，可只测试有代表性的样本，在完成效率与信心之间做平衡。

删除技能不需要的示例文件与目录。初始化脚本在 `scripts/`、`references/`、`assets/` 中生成示例用于展示结构，但多数技能不需要全部保留。

#### Update SKILL.md

**写作规范：**统一使用祈使/不定式风格。

##### Frontmatter

YAML frontmatter 仅写 `name` 与 `description`：

- `name`: The skill name
- `description`: This is the primary triggering mechanism for your skill, and helps Claude understand when to use the skill.
  - 同时包含技能做什么，以及何时使用的触发语境/条件。
  - 所有“何时使用”信息都放在这里，不要放在正文。正文只在触发后加载，因此正文里的 “When to Use This Skill” 对 Claude 触发判断没有帮助。
  - `docx` 技能的示例描述："Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. Use when Claude needs to work with professional documents (.docx files) for: (1) Creating new documents, (2) Modifying or editing content, (3) Working with tracked changes, (4) Adding comments, or any other document tasks"

YAML frontmatter 不要包含其他字段。

##### Body

正文写清楚如何使用技能及其随附资源。

### 步骤 5：打包技能

技能开发完成后，需要打包为可分发的 .skill 文件以便分享给用户。打包流程会先自动校验技能是否符合要求：

```bash
scripts/package_skill.py <path/to/skill-folder>
```

可选：指定输出目录：

```bash
scripts/package_skill.py <path/to/skill-folder> ./dist
```

打包脚本会：

1. **Validate** the skill automatically, checking:

   - YAML frontmatter format and required fields
   - Skill naming conventions and directory structure
   - Description completeness and quality
   - File organization and resource references

2. **Package** the skill if validation passes, creating a .skill file named after the skill (e.g., `my-skill.skill`) that includes all files and maintains the proper directory structure for distribution. The .skill file is a zip file with a .skill extension.

若校验失败，脚本会报告错误并退出且不会生成包。修复所有校验错误后再次运行打包命令。

### 步骤 6：迭代

测试技能后，用户可能会提出改进需求。通常发生在刚使用完技能、对表现仍有鲜活记忆的时候。

**Iteration workflow:**

1. Use the skill on real tasks
2. Notice struggles or inefficiencies
3. Identify how SKILL.md or bundled resources should be updated
4. Implement changes and test again
