---
name: mcp-builder
description: 用于创建高质量 MCP（Model Context Protocol）服务器的指南，使 LLM 能通过设计良好的工具与外部服务交互。构建用于集成外部 API/服务的 MCP server 时使用，不论是 Python（FastMCP）还是 Node/TypeScript（MCP SDK）。
license: Complete terms in LICENSE.txt
---

# MCP Server 开发指南

## 概述

创建 MCP（Model Context Protocol）服务器，使 LLM 能通过设计良好的工具与外部服务交互。MCP server 的质量取决于它能多有效地帮助 LLM 完成真实世界任务。

---

# 流程

## 🚀 高层工作流

创建高质量 MCP server 通常分四个阶段：

### 阶段 1：深入调研与规划

#### 1.1 理解现代 MCP 设计

**API 覆盖 vs 工作流工具：**
在“覆盖尽可能多的 API endpoint”与“提供针对场景的工作流工具”之间做平衡。工作流工具对特定任务更省事；全面覆盖则让 Agent 更灵活地组合操作。不同客户端的表现也不同：有的更适合通过代码执行把基础工具组合起来，有的更适合高层工作流。拿不准时，优先保证 API 覆盖面。

**工具命名与可发现性：**
清晰、描述性强的工具名能让 Agent 更快找到正确工具。使用一致的前缀（如 `github_create_issue`、`github_list_repos`）与动词导向命名。

**上下文管理：**
工具描述要简洁，并支持过滤/分页返回结果。设计应返回聚焦且相关的数据。有些客户端支持代码执行，可帮助 Agent 高效过滤与处理数据。

**可行动的错误信息：**
错误信息应可行动：给出明确建议与下一步。

#### 1.2 学习 MCP 协议文档

**浏览 MCP 规范：**

先从站点地图定位相关页面：`https://modelcontextprotocol.io/sitemap.xml`

然后抓取带 `.md` 后缀的页面以获取 markdown 格式（例如 `https://modelcontextprotocol.io/specification/draft.md`）。

需要重点阅读的页面：
- 规范概览与架构
- 传输机制（streamable HTTP、stdio）
- tool/resource/prompt 的定义

#### 1.3 学习框架文档

**推荐技术栈：**
- **语言**：TypeScript（SDK 支持质量高，且在多种执行环境中兼容性更好，例如 MCPB；同时 TypeScript 使用广、静态类型与 lint 工具成熟，模型也更擅长生成）
- **传输**：远程服务用 Streamable HTTP，并使用无状态 JSON（更易扩缩与维护，优于有状态会话与流式响应）。本地服务用 stdio。

**加载框架文档：**

- **MCP Best Practices**：[📋 查看最佳实践](./reference/mcp_best_practices.md) - 核心指南

**TypeScript（推荐）：**
- **TypeScript SDK**：用 WebFetch 加载 `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
- [⚡ TypeScript Guide](./reference/node_mcp_server.md) - TypeScript 模式与示例

**Python：**
- **Python SDK**：用 WebFetch 加载 `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
- [🐍 Python Guide](./reference/python_mcp_server.md) - Python 模式与示例

#### 1.4 规划实现

**理解 API：**
阅读目标服务的 API 文档，识别关键 endpoint、鉴权要求与数据模型。必要时使用 web search 与 WebFetch。

**工具选型：**
优先保证 API 覆盖面。列出要实现的 endpoint，从最常用操作开始。

---

### 阶段 2：实现

#### 2.1 搭建项目结构

参考语言对应的项目搭建指南：
- [⚡ TypeScript Guide](./reference/node_mcp_server.md) - Project structure, package.json, tsconfig.json
- [🐍 Python Guide](./reference/python_mcp_server.md) - Module organization, dependencies

#### 2.2 实现核心基础设施

创建共享工具：
- 带鉴权的 API client
- 错误处理辅助函数
- 响应格式化（JSON/Markdown）
- 分页支持

#### 2.3 实现工具（Tools）

For each tool:

**Input Schema:**
- Use Zod (TypeScript) or Pydantic (Python)
- Include constraints and clear descriptions
- Add examples in field descriptions

**Output Schema:**
- Define `outputSchema` where possible for structured data
- Use `structuredContent` in tool responses (TypeScript SDK feature)
- Helps clients understand and process tool outputs

**Tool Description:**
- Concise summary of functionality
- Parameter descriptions
- Return type schema

**Implementation:**
- Async/await for I/O operations
- Proper error handling with actionable messages
- Support pagination where applicable
- Return both text content and structured data when using modern SDKs

**Annotations:**
- `readOnlyHint`: true/false
- `destructiveHint`: true/false
- `idempotentHint`: true/false
- `openWorldHint`: true/false

---

### 阶段 3：复核与测试

#### 3.1 代码质量

Review for:
- No duplicated code (DRY principle)
- Consistent error handling
- Full type coverage
- Clear tool descriptions

#### 3.2 构建与测试

**TypeScript:**
- Run `npm run build` to verify compilation
- Test with MCP Inspector: `npx @modelcontextprotocol/inspector`

**Python:**
- Verify syntax: `python -m py_compile your_server.py`
- Test with MCP Inspector

See language-specific guides for detailed testing approaches and quality checklists.

---

### 阶段 4：创建评测（Evaluations）

完成 MCP server 后，创建一套完整的评测用例来测试其有效性。

**Load [✅ Evaluation Guide](./reference/evaluation.md) for complete evaluation guidelines.**

#### 4.1 理解评测目的

评测用于检验 LLM 是否能有效使用你的 MCP server 来回答真实且复杂的问题。

#### 4.2 创建 10 个评测问题

To create effective evaluations, follow the process outlined in the evaluation guide:

1. **Tool Inspection**: List available tools and understand their capabilities
2. **Content Exploration**: Use READ-ONLY operations to explore available data
3. **Question Generation**: Create 10 complex, realistic questions
4. **Answer Verification**: Solve each question yourself to verify answers

#### 4.3 评测要求

Ensure each question is:
- **Independent**: Not dependent on other questions
- **Read-only**: Only non-destructive operations required
- **Complex**: Requiring multiple tool calls and deep exploration
- **Realistic**: Based on real use cases humans would care about
- **Verifiable**: Single, clear answer that can be verified by string comparison
- **Stable**: Answer won't change over time

#### 4.4 输出格式

Create an XML file with this structure:

```xml
<evaluation>
  <qa_pair>
    <question>查找关于以动物代号命名的 AI 模型发布的讨论。其中一个模型需要一种格式为 ASL-X 的安全等级标识。那款以斑点野猫命名的模型当时在确定的 X 是多少？</question>
    <answer>3</answer>
  </qa_pair>
<!-- More qa_pairs... -->
</evaluation>
```

---

# 参考文件

## 📚 文档库

开发过程中按需加载以下资源：

### MCP 核心文档（优先加载）
- **MCP Protocol**：先看站点地图 `https://modelcontextprotocol.io/sitemap.xml`，再抓取具体页面（带 `.md` 后缀）
- [📋 MCP Best Practices](./reference/mcp_best_practices.md) - 通用 MCP 指南，包含：
  - Server 与 tool 命名约定
  - 响应格式指南（JSON vs Markdown）
  - 分页最佳实践
  - 传输方式选择（streamable HTTP vs stdio）
  - 安全与错误处理标准

### SDK 文档（阶段 1/2 加载）
- **Python SDK**：从 `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md` 获取
- **TypeScript SDK**：从 `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md` 获取

### 语言实现指南（阶段 2 加载）
- [🐍 Python Implementation Guide](./reference/python_mcp_server.md) - 完整 Python/FastMCP 指南，包含：
  - Server 初始化模式
  - Pydantic model 示例
  - 使用 `@mcp.tool` 注册工具
  - 完整可运行示例
  - 质量检查清单

- [⚡ TypeScript Implementation Guide](./reference/node_mcp_server.md) - 完整 TypeScript 指南，包含：
  - 项目结构
  - Zod schema 模式
  - 使用 `server.registerTool` 注册工具
  - 完整可运行示例
  - 质量检查清单

### 评测指南（阶段 4 加载）
- [✅ Evaluation Guide](./reference/evaluation.md) - 完整评测创建指南，包含：
  - 问题编写指南
  - 答案校验策略
  - XML 格式规范
  - 示例问答
  - 使用随附脚本运行评测
