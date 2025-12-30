---
name: brand-guidelines
description: 将 Anthropic 官方品牌色与字体规范应用到任何需要 Anthropic 视觉风格的产物中。在涉及品牌色/风格指南、视觉排版或公司设计标准时使用。
license: Complete terms in LICENSE.txt
---

# Anthropic 品牌样式

## 概述

需要获取并应用 Anthropic 官方品牌识别与样式资源时，使用此技能。

**关键词**：branding, corporate identity, visual identity, post-processing, styling, brand colors, typography, Anthropic brand, visual formatting, visual design

## 品牌规范

### 颜色

**主色：**

- Dark: `#141413` - 主文本与深色背景
- Light: `#faf9f5` - 浅色背景与深色背景上的文字
- Mid Gray: `#b0aea5` - 次要元素
- Light Gray: `#e8e6dc` - 轻量背景与弱化区域

**强调色：**

- Orange: `#d97757` - 主强调色
- Blue: `#6a9bcc` - 次强调色
- Green: `#788c5d` - 第三强调色

### 字体

- **标题**：Poppins（不可用时回退 Arial）
- **正文**：Lora（不可用时回退 Georgia）
- **说明**：为获得最佳效果，建议在环境中预先安装字体

## 功能

### 智能字体应用

- 对标题（24pt 及以上）应用 Poppins
- 对正文应用 Lora
- 自定义字体不可用时自动回退到 Arial/Georgia
- 在不同系统中保持可读性

### 文本样式

- 标题（24pt+）：Poppins
- 正文：Lora
- 根据背景智能选择文字颜色
- 保留文本层级与格式

### 图形与强调色

- 非文本图形使用强调色
- 在橙/蓝/绿强调色之间循环
- 在保持品牌一致性的同时增加视觉层次

## 技术细节

### 字体管理

- 可用时使用系统已安装的 Poppins 与 Lora
- 自动回退到 Arial（标题）与 Georgia（正文）
- 无需强制安装字体，也能使用系统字体正常工作
- 为获得最佳效果，建议预先安装 Poppins 与 Lora

### 颜色应用

- 使用 RGB 颜色值以精准匹配品牌色
- 通过 python-pptx 的 RGBColor 类应用颜色
- 在不同系统中保持颜色一致性
