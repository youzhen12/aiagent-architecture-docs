---
title: "基于 Prometheus + Grafana + 现有日志平台的方案"
status: draft
---

# 描述

使用 Prometheus 采集和存储指标，Grafana 展示与告警，日志继续通过现有集中式日志平台（如 Elasticsearch/OpenSearch 或云日志服务）收集；追踪（Tracing）基于 OpenTelemetry 与现有 APM 集成。

# 优点

- 与现有运维体系对齐：
  - 大多数云原生环境都已采用 Prometheus/Grafana 组合，团队有经验，成本低。
- 开源生态成熟：
  - 指标采集、可视化、告警、导出器生态完善，可按需扩展。
- 可移植性强：
  - 不绑定某一家闭源 SaaS，支持在多云或自建环境下部署。

# 缺点

- 需要在服务中主动埋点：
  - 每个 Agent/服务需要显式暴露指标端点，并为业务关键路径设计指标与 Trace span。
- 初始配置工作量较大：
  - 需要设计 Dashboard、告警规则以及采集配置，短期投入较高。

# 使用 Prometheus + Grafana 的开源项目示例

- Kubernetes 及其周边组件：
  - 大量云原生项目默认使用 Prometheus/Grafana 作为监控与可视化方案。
- Qdrant、Milvus 等数据库项目：
  - 官方都提供 Prometheus 指标支持，用于监控查询延迟、资源占用等。
- 对比本仓库项目：
  - `aiguides` 等 Go 服务可以通过标准 Prometheus 指标库暴露 HTTP 指标，和本方案天然契合。
