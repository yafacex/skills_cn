---
name: internal-comms
description: 一组用于撰写各类公司内部沟通稿的资源，遵循公司偏好的写作格式。凡是被要求撰写内部沟通（状态报告、领导层更新、3P 更新、公司通讯、FAQ、事故复盘、项目更新等）时，Claude 都应使用此技能。
license: Complete terms in LICENSE.txt
---

## 何时使用此技能
撰写以下内部沟通内容时使用此技能：
- 3P updates (Progress, Plans, Problems)
- 公司通讯/Newsletter
- FAQ 回复
- 状态报告
- 领导层更新
- 项目更新
- 事故报告/复盘

## 如何使用此技能

撰写任何内部沟通稿时：

1. 从需求中**识别沟通类型**
2. 从 `examples/` 目录中**加载对应的指南文件**：
    - `examples/3p-updates.md` - 用于 Progress/Plans/Problems 的团队更新
    - `examples/company-newsletter.md` - 用于公司范围的 Newsletter
    - `examples/faq-answers.md` - 用于常见问题（FAQ）回复
    - `examples/general-comms.md` - 用于不完全匹配以上类型的其他沟通
3. 按该文件中的具体要求执行：包括格式、语气与信息收集

如果沟通类型与现有任何指南都不匹配，先询问对方期望的格式或补充上下文。

## 关键词
3P updates, company newsletter, company comms, weekly update, faqs, common questions, updates, internal comms
