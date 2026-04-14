---
title: 行业场景与文档映射总览
status: draft
version: 0.2.0
owner: [负责人]
last_updated: 2026-04-14
tags: [landscape, scenes, index]
---

# 行业场景与文档映射总览

> 本文档提供「行业/赛道 → 典型场景 → 关联文档」的索引视图，是 MaaS 方案资产的导航入口。
> 
> **相关文档**：[README](readme.md) | [POC 模板](maas-poc-playbook-temp.md) | [阿里云产品映射](aliyun-product-feedback-notes.md)

## 1. 场景分组总览（按 SaaS / 行业赛道）

- SaaS / 企业软件（ERP / CRM / OA / HR / 项目管理等）：
  - SaaS 平台内嵌 Agent / 管理后台助手：
    - 架构：`scene-saas-platform-agent/architecture-overview.md`
    - AI 需求与价值：`scene-saas-platform-agent/ai-application-overview.md`
    - MaaS 方案：`scene-saas-platform-agent/maas-solution-design.md`
  - 典型子场景提示：新租户上手向导、权限/流程配置诊断、运维/FinOps 分析助手、变更影响评估等。

- 客服与服务云：
  - 智能客服 / 联络中心：
    - 架构：`scene-customer-support/architecture-overview.md`
    - AI 需求与价值：`scene-customer-support/ai-application-overview.md`
    - MaaS 方案：`scene-customer-support/maas-solution-design.md`
  - 典型子场景提示：FAQ 自助问答、订单/账务查询、复杂问题升级单生成、自动质检和风险对话识别等。

- 零售 & 电商：
  - 秒杀 / 大促旁路 AIAgent：
    - 架构：`scene-flash-sale-platform/architecture-overview.md`
    - AI 需求与价值：`scene-flash-sale-platform/ai-application-overview.md`
    - MaaS 方案：`scene-flash-sale-platform/maas-solution-design.md`
  - 典型子场景提示：活动配置检查、实时异常巡检、活动复盘报告生成、运营问答助手等。

- 开发者工具 / AI 原生工具链：
  - Coding OS / 开发者智能体工作台（类似 Claude Code）：
    - 架构：`scene-claude-code-assistant/architecture-overview.md`
    - AI 需求与价值：`scene-claude-code-assistant/ai-application-overview.md`
    - MaaS 方案：`scene-claude-code-assistant/maas-solution-design.md`
  - 典型子场景提示：代码补全与重构、跨仓库代码/文档检索、CI 失败排查、变更摘要与 Release Note 生成等。

- 个人助手与学习场景：
  - 个人助理 / 工作流助手：
    - 架构：`scene-personal-assistant/architecture-overview.md`
    - AI 需求与价值：`scene-personal-assistant/ai-application-overview.md`
  - 初学者 AIAgent Playground：
    - 架构：`scene-beginner-playground/architecture-overview.md`
    - AI 需求与价值：`scene-beginner-playground/ai-application-overview.md`
  - 典型子场景提示：日程管理、邮件总结、资料整理、学习路径规划等。

- 金融 / 医疗 / 制造等垂直行业：
  - 金融：如智能投顾助手、风控规则分析、合规文档问答等。
  - 医疗：如病历摘要、随访问卷整理、医学知识检索等。
  - 制造：如设备告警分析、工单自动归因、质量问题复盘等。
  - 若后续落地具体场景，可在 `scene-*/` 目录新建对应场景，并补充架构 / AI 需求 / MaaS 方案文档。

## 2. 通用决策与工程主线

- 通用决策目录（与所有场景共享）：
  - 向量数据库选型：`aia-agent-architecture/decisions/0001-vector-db-selection/`
  - Agent 编排框架选型：`aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/`
  - 模型与推理服务选型：`aia-agent-architecture/decisions/0003-model-and-inference-service-selection/`
  - 可观测性与日志方案选型：`aia-agent-architecture/decisions/0004-observability-and-logging-selection/`
  - 工程设计（Prompt/Context/Harness）：`aia-agent-architecture/decisions/0005-engineering-design/`
  - 大模型厂商与版本选型：`aia-agent-architecture/decisions/0006-foundation-models-selection/`
  - Agent 测试与评估方案选型：`aia-agent-architecture/decisions/0007-agent-testing-strategy-selection/`

## 3. 视角映射

- 「深入研究不同行业SaaS/AI原生软件」：
  - 上述场景分组中的 SaaS 平台、客服云、零售电商、大促、开发者工具等，分别对应 JD 中的 ERP/CRM/OA、客服、零售/营销、办公与开发工具赛道。
- 「梳理各场景 AI 需求和价值」：
  - 对应 `scene-*/ai-application-overview.md`，以及本文件中为每组场景列出的典型子场景提示，可作为「场景清单」的入口。
- 「端到端 MaaS 解决方案」：
  - 对应 `scene-*/maas-solution-design.md` + [maas-poc-playbook-temp.md](maas-poc-playbook-temp.md) + 各场景下的 POC 计划与案例文档。
- 「能力沉淀和赋能」：
  - 本文件负责给出「行业 / 赛道 → 典型场景清单 → 关联文档」的索引视图，配合 [aliyun-scene-mapping.md](aliyun-scene-mapping.md)（场景映射）和 [aliyun-product-feedback.md](aliyun-product-feedback.md)（产品反馈）形成完整资产。

## 4. 相关文档

| 文档 | 用途 |
|------|------|
| [README](readme.md) | 入口导航与使用指南 |
| [POC 模板](maas-poc-playbook-temp.md) | 端到端 POC 设计模板 |
| [场景映射](aliyun-scene-mapping.md) | 阿里云产品映射指南 |
| [产品反馈](aliyun-product-feedback.md) | 产品机会点汇总 |
| [标杆案例](cases/) | 场景实施案例 |
| [变更日志](CHANGELOG.md) | 版本变更记录 |