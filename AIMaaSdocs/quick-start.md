---
title: AI MaaS 文档快速开始指南
status: draft
version: 0.1.0
owner: [负责人]
last_updated: 2026-04-14
tags: [guide, quick-start, docs]
---

# 快速开始指南

> 本文档帮助你快速了解和使用 AIMaaSdocs 知识库。

## 1. 了解文档定位

AIMaaSdocs 是 AI MaaS（Model-as-a-Service）解决方案的场景资产库，包含：

- 行业场景索引
- 端到端 POC 模板与计划
- 阿里云产品映射
- 标杆案例草稿

## 2. 按角色阅读

### 售前/销售人员

**目标**：了解方案覆盖范围，准备客户沟通材料

1. 阅读 [场景族谱](scene-landscape.md) 了解已覆盖的行业场景
2. 查阅 [标杆案例](cases/) 获取可展示的实施经验
3. 参考 [阿里云产品映射](aliyun-scene-mapping.md) 了解技术基座

### 解决方案架构师

**目标**：设计 MaaS 方案，进行产品映射

1. 从 [场景族谱](scene-landscape.md) 找到目标场景
2. 阅读对应 `scene-*/` 目录下的架构文档
3. 参考 [阿里云产品映射](aliyun-scene-mapping.md) 进行产品选型
4. 使用 [POC 模板](maas-poc-playbook-temp.md) 设计验证计划

### 实施工程师

**目标**：执行 POC，交付方案

1. 复制 [POC 模板](maas-poc-playbook-temp.md) 创建场景 POC 计划
2. 参考 [标杆案例](cases/) 中的架构亮点和实现要点
3. 按照 POC 计划的阶段逐步执行
4. 使用 [产品反馈文档](aliyun-product-feedback.md) 中的模式（P1-P5）指导实施

## 3. 快速上手步骤

### 场景 A：为新客户设计 MaaS 方案

```
1. 浏览场景族谱 → scene-landscape.md
2. 选择最接近的场景 → 阅读对应 scene-*/ 文档
3. 复制 POC 模板 → maas-poc-playbook-temp.md
4. 参考阿里云映射 → aliyun-scene-mapping.md
5. 查看类似案例 → cases/
```

### 场景 B：准备 POC 实施

```
1. 阅读 POC 模板 → maas-poc-playbook-temp.md
2. 参考场景 POC 计划 → maas-poc-plan-*.md
3. 查看案例中的技术方案 → cases/*.md
4. 使用产品反馈中的模式 → aliyun-product-feedback.md
```

### 场景 C：新增一个 MaaS 场景

```
1. 在 docs/scene-xxx/ 创建场景文档
2. 在 scene-landscape.md 添加场景条目
3. 复制 POC 模板创建 maas-poc-plan-xxx.md
4. 实施后创建 cases/xxx-maas-case.md
```

## 4. 文档结构速览

```
AIMaaSdocs/
├── readme.md                          # 入口导航（推荐首先阅读）
├── CHANGELOG.md                       # 版本变更记录
├── scene-landscape.md                 # 场景族谱索引
├── maas-poc-playbook-temp.md          # POC 设计模板
├── aliyun-product-feedback-notes.md   # 汇总页（索引）
├── aliyun-scene-mapping.md            # 场景→产品映射
├── aliyun-product-feedback.md         # 产品机会点
├── cases/                             # 标杆案例
│   ├── customer-support-maas-case.md  # 客服案例
│   └── saas-platform-maas-case.md     # SaaS 平台案例
├── maas-poc-plan-customer-support.md  # 客服 POC 计划
└── maas-poc-plan-saas-platform.md     # SaaS POC 计划
```

## 5. 常用链接

| 资源 | 路径 |
|------|------|
| 架构决策 | `../aia-agent-architecture/decisions/` |
| 场景模板 | `../aia-agent-architecture/templates/` |
| 术语表 | `../glossary.md` |
| 主文档 | `../readme.md` |

## 6. 获取帮助

- 不确定术语含义：查阅 `../glossary.md`
- 需要了解技术选型：查阅 `../aia-agent-architecture/decisions/000x-*.md`
- 需要修改文档规范：参考 [README](readme.md) 第 4 节
