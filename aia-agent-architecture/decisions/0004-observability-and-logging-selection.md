---
title: "监控与日志方案选型（0004）"
status: accepted
date: 2026-04-02
decision_id: 0004
owner: [待定]
tags: [adr, observability, logging, tracing]
---

# 背景

AIAgent 涉及多 Agent、多工具、多外部服务（LLM、向量库等），需要较强的可观测性来支持排障、性能分析和安全审计。

# 问题与目标

- 指标：监控延迟、错误率、QPS、资源使用等，并支持告警。
- 日志：统一结构化日志，能按请求/会话/Agent 维度检索。
- Tracing：能够追踪一次请求在编排层、Agent、工具、外部服务之间的调用链。

# 约束条件

- 现有运维体系已使用 Prometheus/Grafana 和集中式日志平台；
- 希望优先复用已有基础设施，而非完全引入新的 SaaS 平台。

# 备选方案概览

> 详细对比见：`aia-agent-architecture/decisions/0004-observability-and-logging-selection/README.md`

- 仅聚合日志（logs-only）
- Prometheus + Grafana + 日志平台
- 引入 SaaS Observability 平台

# 决策

- 采用方案：**Prometheus + Grafana + 现有日志平台 + OpenTelemetry Tracing**。

# 选择原因（简要）

- 最大化复用现有基础设施和团队经验；
- 保持开源与可迁移性，避免深度供应商锁定；
- 支持按需逐步增加指标和 tracing，适合从简单到复杂的演进路径。

# 架构影响

- 所有核心服务：
  - 暴露 Prometheus 指标端点；
  - 使用统一的日志字段（request_id、trace_id、agent_name 等）。
- 在编排层与工具层接入 OpenTelemetry SDK，形成端到端调用链。

# 风险与缓解

- 风险：埋点不足导致可观测性信息缺失。
  - 缓解：从少量关键路径开始；在每次事故复盘中补齐监控项。
- 风险：指标与日志量过大带来成本问题。
  - 缓解：控制采样率与保留周期；对低价值日志做降采样或聚合。

# 实施与演进计划

- 阶段 1：基础可观测性
  - 为核心服务添加基础指标和 tracing；
  - 建立初始 Grafana Dashboard；
  - 统一日志格式。
- 阶段 2：精细化
  - 为关键 Agent 和工具调用增加独立指标与 span；
  - 以「事故驱动」方式迭代完善监控与告警。

---

> 相关对比材料：
> - `aia-agent-architecture/decisions/0004-observability-and-logging-selection/README.md`
> - `logs-only.md`、`prometheus-grafana.md`、`saas-observability.md` 等细化文档
