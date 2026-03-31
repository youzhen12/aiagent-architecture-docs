---
title: "基于 Prometheus + Grafana + 现有日志平台的方案"
status: draft
---

# 描述

使用 Prometheus 采集和存储指标，Grafana 展示与告警，日志继续通过现有集中式日志平台（如 Elasticsearch/OpenSearch 或云日志服务）收集；追踪（Tracing）基于 OpenTelemetry 与现有 APM 集成。Prometheus/Grafana 作为「基础设施级观测骨干」，再叠加 LLM/Agent 专用观测工具，可以构成较为完整的观测体系。

# 优点

- 与现有运维体系对齐：
  - 大多数云原生环境都已采用 Prometheus/Grafana 组合，团队有经验，成本低。
- 开源生态成熟：
  - 指标采集、可视化、告警、导出器生态完善，可按需扩展。
- 可移植性强：
  - 不绑定某一家闭源 SaaS，支持在多云或自建环境下部署。
- 便于承载 LLM/Agent 的基础观测：
  - 可以通过自定义指标与 Trace span，将 Prompt、模型调用、工具调用等关键事件打点进 Prometheus/OpenTelemetry，从而与 0005 中的驭控工程形成闭环。

# 缺点

- 需要在服务中主动埋点：
  - 每个 Agent/服务需要显式暴露指标端点，并为业务关键路径设计指标与 Trace span。
- 初始配置工作量较大：
  - 需要设计 Dashboard、告警规则以及采集配置，短期投入较高。
- 对 LLM 专用分析仍需额外组件：
  - Prometheus/Grafana 更擅长做数值指标与基础设施监控，对 Prompt 级别的质量评估、对话内容分析等仍然需要结合专门的 LLM 观测平台或自建分析流程。

# 使用 Prometheus + Grafana 的开源项目示例

- Kubernetes 及其周边组件：
  - 大量云原生项目默认使用 Prometheus/Grafana 作为监控与可视化方案。
- Qdrant、Milvus 等数据库项目：
  - 官方都提供 Prometheus 指标支持，用于监控查询延迟、资源占用等。
- 对比本仓库项目：
  - `aiguides` 等 Go 服务可以通过标准 Prometheus 指标库暴露 HTTP 指标，和本方案天然契合。
