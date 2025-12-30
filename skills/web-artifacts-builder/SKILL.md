---
name: web-artifacts-builder
description: 使用现代前端技术（React、Tailwind CSS、shadcn/ui）创建复杂、多组件的 claude.ai HTML 产物的一整套工具。适用于需要状态管理、路由或 shadcn/ui 组件的复杂产物；不适用于简单的单文件 HTML/JSX 产物。
license: Complete terms in LICENSE.txt
---

# Web Artifacts Builder

构建强大的前端 claude.ai 产物时，按以下步骤执行：
1. 使用 `scripts/init-artifact.sh` 初始化前端项目
2. 编辑生成的代码，开发产物
3. 使用 `scripts/bundle-artifact.sh` 将所有代码打包为单个 HTML 文件
4. 向用户展示产物
5. （可选）测试产物

**Stack**: React 18 + TypeScript + Vite + Parcel (bundling) + Tailwind CSS + shadcn/ui

## Design & Style Guidelines

非常重要：为避免常被称作“AI 套模板审美”的效果，避免过度居中布局、紫色渐变、统一的圆角套路以及 Inter 字体。

## Quick Start

### Step 1: Initialize Project

运行初始化脚本创建新的 React 项目：
```bash
bash scripts/init-artifact.sh <project-name>
cd <project-name>
```

脚本会创建一个已完整配置的项目，包含：
- ✅ React + TypeScript (via Vite)
- ✅ Tailwind CSS 3.4.1 with shadcn/ui theming system
- ✅ 路径别名（`@/`）已配置
- ✅ 40+ 个 shadcn/ui 组件已预装
- ✅ 已包含全部 Radix UI 依赖
- ✅ 已配置 Parcel 打包（通过 .parcelrc）
- ✅ Node 18+ 兼容（自动检测并锁定 Vite 版本）

### Step 2: Develop Your Artifact

编辑生成的文件以构建产物。可参考下方 **Common Development Tasks** 的说明。

### Step 3: Bundle to Single HTML File

将 React 应用打包为单个 HTML 产物：
```bash
bash scripts/bundle-artifact.sh
```

该脚本会生成 `bundle.html`：一个自包含产物，所有 JavaScript、CSS 与依赖均已内联。可直接在与 Claude 的对话中作为 artifact 分享。

**要求**：项目根目录必须有 `index.html`。

**脚本做了什么**：
- 安装打包依赖（parcel、@parcel/config-default、parcel-resolver-tspaths、html-inline）
- 创建支持路径别名的 `.parcelrc` 配置
- 使用 Parcel 构建（不生成 source map）
- 使用 html-inline 将所有资源内联到单个 HTML

### Step 4: Share Artifact with User

最后，在对话中把打包后的 HTML 文件分享给用户，使其以 artifact 形式查看。

### Step 5: Testing/Visualizing the Artifact (Optional)

注意：此步骤完全可选，仅在必要或被请求时执行。

测试/可视化产物时，可使用可用工具（包括其他技能或 Playwright、Puppeteer 等内置工具）。一般不要一开始就测试，因为会增加从需求到展示成品的等待时间。优先先交付产物；若被要求或出现问题，再进行测试。

## Reference

- **shadcn/ui components**: https://ui.shadcn.com/docs/components
