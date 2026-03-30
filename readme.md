
## 项目简介

本项目是一个面向开发者的架构设计参考，目标是：

- 总结AIAgent相关技术和架构方案
- 支持不同业务场景下的 AI Agent 解决方案快速搭建与对比


## 文档结构

- `readme.md`：当前文件，文档入口和整体说明
- `aia-agent-architecture/`
  - `decisions/`：现有技术与方案的介绍与对比
    - 每个文件聚焦一种技术 / 组件，例如：对话模型、向量数据库、工作流编排框架、工具调用框架等
    - 内容建议包含：适用场景、优缺点、与本项目的集成方式、实际踩坑记录
  - `architecture-overview-temp.md`：统一的架构文档模板示例
    - 主要用于说明在某个场景下的整体架构和各类技术选型
    - 其中需要写明：每一种技术选择的原因、被放弃方案的对比、未来可替代/演进方向
- `*/`（其他子目录）：具体业务/使用场景下的架构文档
  - 每个子目录代表一个场景（例如：本地 IDE 助手、代码审查 Agent、知识库问答系统等）
  - 建议在每个场景目录中包含：场景描述、需求与约束、架构总览、关键技术选型与权衡、运行/部署说明、监控与评估指标等

### 现有 `decisions/` 子目录说明

当前已存在的四个决策目录分别用于：

- `0001-vector-db-selection/`：向量数据库选型，例如 Postgres + pgvector、Qdrant、Milvus 等，在这里对比检索性能、运维复杂度、成本与生态。
- `0002-agent-orchestration-framework-selection/`：智能体编排框架选型，如 LangGraph、Custom Orchestrator、CrewAI / AutoGen 等，用于比较易用性、可观测性、可扩展性和与现有系统的集成方式。
- `0003-model-and-inference-service-selection/`：模型与推理服务选型，涵盖直连厂商 API、自建 / 第三方推理网关等，主要关注成本、性能、可用性、多模型切换能力等维度。
- `0004-observability-and-logging-selection/`：可观测性与日志方案选型，例如仅日志、Prometheus + Grafana、自建或 SaaS 观测平台，用于评估在不同规模下的监控告警、日志分析与运维体验。

## 使用建议

- 新增技术调研或选型时：
  - 在 `aia-agent-architecture/decisions/` 下新建一个目录或对应目录下的文件，记录该技术/方案的认知和结论
- 针对特定落地场景做设计时：
  - 新增一个场景目录，复制 `aia-agent-architecture/architecture-overview-temp.md` 作为模板进行填充
- 当项目演进或技术更替时：
  - 优先更新对应的 `decisions/` 文档及相关场景架构文档，并在文档中明确「变更原因」和「影响范围」。

欢迎大家一起维护、更新
