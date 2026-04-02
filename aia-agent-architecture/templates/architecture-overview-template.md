---
title: "通用架构总览模板"
status: stable
version: 0.1.0
owner: [文档维护人]
last_updated: 2026-04-02
---

> 用于：
> - `aia-agent-architecture/overview` 级别的整体架构说明；
> - 或特定场景下的架构总览（可在 `scene-*/` 中复制使用）。

# 1. 目标与范围

- 本文档目标：
- 不在本文档范围内：

# 2. 业务与场景概览

- 主要用户与角色：
- 核心使用场景（Top 3）：

# 3. 整体架构视图

- 一句话描述整体架构风格：
- 架构图占位：
  - [ ] 总体逻辑架构图
  - [ ] 部署架构图

## 3.1 核心组件

- Orchestrator / Router：
- Agent Registry：
- Tools / Connectors：
- Memory / Knowledge：
- Observability：

# 4. 数据与知识

- 知识来源（代码库、文档、API 等）：
- 向量库 / 检索方案：
- 数据生命周期与更新策略：

# 5. 关键技术选型

- 模型与推理服务：参考 `decisions/0003-*`、`0006-*`
- 向量库与检索：参考 `decisions/0001-*`
- Agent 编排框架：参考 `decisions/0002-*`
- 监控与日志方案：参考 `decisions/0004-*`

# 6. 非功能性需求

- 性能与延迟目标：
- 可用性与容错：
- 可观测性（日志 / 指标 / Tracing）：
- 安全与合规要求：

# 7. 演进路线

- 当前阶段能力：
- 下一阶段规划：
- 可能的技术债与未来重构点：
