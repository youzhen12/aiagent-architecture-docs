---
title: "场景级架构总览模板"
status: stable
version: 0.1.0
owner: [文档维护人]
last_updated: 2026-04-02
scene_id: scene-xxx
---

> 用途：
> - 作为 `docs/scene-*/architecture-overview.md` 的统一骨架；
> - 帮助在不同场景间保持章节结构一致，便于横向对比。

# 1. 背景与目标

- 场景简介：
- 业务问题：
- AIAgent 目标：
- 文档目标：

# 2. 业务与使用场景

- 主要用户与角色：
- 关键使用场景（列 3–5 个）：
- 典型业务流程（1–2 条）：

# 3. 整体架构概览

- 一句话描述整体架构风格：
- 架构图占位：
  - [ ] 逻辑架构图
  - [ ] 部署架构图

- 核心组件清单（简要）：
  - 用户入口（CLI/Web/IDE 等）：
  - Orchestrator / Agent 编排层：
  - Agent 集合：
  - 工具层：
  - 模型与向量库：
  - 监控与日志：

# 4. 核心技术选型与原因

> 本节给出「本场景实际选型」；
> `aia-agent-architecture/decisions/000x-*` 中是通用对比材料。

## 4.1 模型与推理服务

- 选型结论：
- 选择原因：
- 参考决策文档：
  - `aia-agent-architecture/decisions/0003-model-and-inference-service-selection.md`
  - `aia-agent-architecture/decisions/0006-foundation-models-selection.md`

## 4.2 向量数据库与检索方案

- 选型结论：
- 选择原因：
- 参考决策文档：
  - `aia-agent-architecture/decisions/0001-vector-db-selection.md`

## 4.3 Agent 编排框架

- 选型结论：
- 选择原因：
- 参考决策文档：
  - `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection.md`

## 4.4 可观测性与日志方案

- 选型结论：
- 选择原因：
- 参考决策文档：
  - `aia-agent-architecture/decisions/0004-observability-and-logging-selection.md`

## 4.5 工程设计重点

- 提示词工程：
- 上下文工程：
- 驯控工程 / Harness：
- 参考决策文档：
  - `aia-agent-architecture/decisions/0005-engineering-design.md`

# 5. 核心组件与流程

- 组件清单与职责：
- 关键流程说明（2–3 条）：

# 6. 数据与存储设计

- 数据类型与来源：
- 存储与索引方案：
- 数据生命周期：

# 7. 非功能性要求

- 性能与延迟：
- 可用性与容错：
- 安全与合规：
- 成本与资源：

# 8. 风险、边界与取舍

- 已知风险：
- 边界与不做的事：
- 关键取舍说明：

# 9. 实施计划（可选）

- 版本拆分：
- 依赖与前置条件：
