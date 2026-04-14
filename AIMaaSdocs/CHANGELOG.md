---
title: AI MaaS 文档变更日志
status: draft
version: 0.1.0
owner: [负责人]
last_updated: 2026-04-14
tags: [changelog, docs]
---

# 变更日志

## [0.2.0] - 2026-04-14

### 新增
- 优化 `readme.md`，增加完整导航表格、推荐阅读路径和使用指南
- 创建本 CHANGELOG.md 文件
- 统一所有文档的 Frontmatter 规范（添加 `tags` 字段，统一 `last_updated` 格式）

### 修复
- 修复 `scene-landscape.md` 和 `maas-poc-playbook-temp.md` 中的占位符 `YYYY-MM-DD`
- 统一 POC 计划文档的 frontmatter 字段（`scenario` → `scene_id`）
- 移除案例文档标题中的"草稿"字样（状态已在 frontmatter 中标注）

### 改进
- 在文档间增加交叉引用链接
- 优化 readme.md 结构，按角色和场景提供导航

## [0.1.0] - 2026-04-02

### 初始版本
- 建立 AIMaaSdocs 目录结构
- 初始文档：
  - `scene-landscape.md` - 场景族谱
  - `maas-poc-playbook-temp.md` - POC 模板
  - `aliyun-product-feedback-notes.md` - 阿里云产品映射
  - `maas-poc-plan-customer-support.md` - 客服 POC 计划
  - `maas-poc-plan-saas-platform.md` - SaaS 平台 POC 计划
  - `cases/customer-support-maas-case.md` - 客服案例
  - `cases/saas-platform-maas-case.md` - SaaS 平台案例
