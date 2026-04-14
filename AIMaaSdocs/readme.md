---
title: AI MaaS 场景与方案知识库
status: draft
version: 0.2.0
owner: [负责人]
last_updated: 2026-04-14
tags: [maas, solutions, poc, cases]
---

# AI MaaS 场景与方案知识库

> 面向 AI MaaS 解决方案的场景架构、POC 计划与标杆案例资产库

## 快速导航

| 文档 | 用途 | 适用角色 |
|------|------|----------|
| [场景族谱](scene-landscape.md) | 行业场景与文档映射总览 | 售前、方案架构师 |
| [POC 模板](maas-poc-playbook-temp.md) | 端到端 MaaS POC 设计模板 | 实施工程师、售前 |
| [阿里云产品映射](aliyun-product-feedback-notes.md) | Qwen/百炼能力映射与产品机会 | 解决方案架构师 |
| [标杆案例](cases/) | 按场景整理的 MaaS 实施案例 | 销售、交付团队 |

## 1. 仓库定位

本目录是在原有 `aia-agent-architecture/` 文档之上的「二次包装」：

- **面向 AI MaaS 解决方案**，把已有场景架构与决策沉淀为可对外展示的方案资产
- **强调**：行业/场景洞察 → 端到端 MaaS 方案 → POC 测试策略 → 标杆案例

### 结构概览

```
AIMaaSdocs/
├── readme.md                          # 当前文件，入口导航
├── scene-landscape.md                 # 行业场景族谱与文档映射
├── maas-poc-playbook-temp.md          # 端到端 MaaS POC 模板
├── aliyun-product-feedback-notes.md   # 阿里云产品映射与反馈
├── cases/                             # 标杆案例库
│   ├── customer-support-maas-case.md
│   └── saas-platform-maas-case.md
└── POC 计划（按场景）
    ├── maas-poc-plan-customer-support.md
    └── maas-poc-plan-saas-platform.md
```

## 2. 与原有文档的关系

### 架构与决策（原有）

| 类型 | 路径 |
|------|------|
| 通用决策 | `aia-agent-architecture/decisions/0001-0010/` |
| 场景架构 | `scene-*/architecture-overview.md` |
| AI 需求与价值 | `scene-*/ai-application-overview.md` |
| MaaS 方案 | `scene-*/maas-solution-design.md` |

### 本目录（MaaS 视角）

| 原文档 | 本目录对应 |
|--------|------------|
| `scene-*/architecture-overview.md` | [scene-landscape.md](scene-landscape.md) 场景索引 |
| `scene-*/maas-solution-design.md` | [cases/](cases/) 案例 + POC 计划 |
| `decisions/` 技术选型 | [aliyun-product-feedback-notes.md](aliyun-product-feedback-notes.md) 产品映射 |

> **提示**：
> - 如需修改底层技术选型或架构，请回到 `aia-agent-architecture/decisions/000x-*.md` 与对应 `scene-*/architecture-overview.md`
> - 如需新增业务场景，请先在 `docs/scene-*/` 下完善场景文档，再在本目录补充 MaaS 视角

## 3. 推荐阅读路径

### 按角色

| 角色 | 推荐路径 |
|------|----------|
| 售前/销售 | [scene-landscape.md](scene-landscape.md) → [cases/](cases/) |
| 方案架构师 | [scene-landscape.md](scene-landscape.md) → [aliyun-product-feedback-notes.md](aliyun-product-feedback-notes.md) → POC 模板 |
| 实施工程师 | [POC 模板](maas-poc-playbook-temp.md) → 场景 POC 计划 → [cases/](cases/) |

### 按场景

| 场景 | 架构文档 | MaaS 案例 | POC 计划 |
|------|----------|-----------|----------|
| SaaS 平台 | `scene-saas-platform-agent/` | [SaaS 案例](cases/saas-platform-maas-case.md) | [POC 计划](maas-poc-plan-saas-platform.md) |
| 智能客服 | `scene-customer-support/` | [客服案例](cases/customer-support-maas-case.md) | [POC 计划](maas-poc-plan-customer-support.md) |
| 秒杀/大促 | `scene-flash-sale-platform/` | 待补充 | 待补充 |
| Coding OS | `scene-claude-code-assistant/` | 待补充 | 待补充 |

## 4. 文档规范

### Frontmatter 字段

| 字段 | 说明 | 示例 |
|------|------|------|
| `title` | 文档标题 | 智能客服场景 AI MaaS 案例 |
| `status` | 文档状态 | `draft` / `reviewing` / `accepted` / `deprecated` |
| `version` | 语义化版本号 | `0.2.0` |
| `owner` | 维护人/角色 | 张三 / 方案架构组 |
| `last_updated` | 更新日期 | `2026-04-14` |
| `tags` | 标签数组 | `[maas, poc, case]` |

### 文档状态流转

```
draft → reviewing → accepted → deprecated/retired
```

## 5. 如何使用

### 快速开始

- **新用户**：阅读 [快速开始指南](quick-start.md) 获取按角色的阅读指引
- **了解场景覆盖**：阅读 [scene-landscape.md](scene-landscape.md)
- **设计 POC**：复制 [maas-poc-playbook-temp.md](maas-poc-playbook-temp.md) 并针对场景定制
- **参考案例**：查阅 [cases/](cases/) 中类似场景的实施经验
- **产品映射**：如需使用阿里云产品，参考 [场景映射指南](aliyun-scene-mapping.md)

### 新增 MaaS 场景

1. 在 `docs/scene-xxx/` 下创建场景文档（架构/AI 需求/MaaS 方案）
2. 在 [scene-landscape.md](scene-landscape.md) 中添加场景条目
3. 复制 POC 模板，创建 `maas-poc-plan-xxx.md`
4. 如已有实施案例，在 [cases/](cases/) 中添加案例文档

## 6. 变更日志

详见 [CHANGELOG.md](CHANGELOG.md)
