---
title: "向量数据库选型（0001）"
status: accepted
date: 2026-04-02
decision_id: 0001
owner: [待定]
tags: [adr, vector-db, rag, storage]
---

# 背景

AIAgent 需要为代码、文档、运行日志等多种知识源提供检索增强生成（RAG）能力，底层依赖向量索引与相似度搜索。
本 ADR 记录在多种主流向量数据库方案中做技术选型的结论，用于指导后续落地与场景设计。

# 问题与目标

- 需要一个可扩展的向量检索方案，支持多场景、多索引（按项目/环境/数据域划分）。
- 需要兼顾早期易用性（本地开发简单）与中长期扩展性（数据量增长后的性能与容量）。
- 希望复用主流 RAG 框架（LangChain/LlamaIndex/LangGraph 等）的生态与适配器。

成功标准：
- 短期内，单机或小规模集群即可满足场景 PoC / 小规模生产使用。
- 中长期可以平滑扩展或迁移，而不是被早期选型锁死。

# 约束条件

- 现有技术栈中已经使用 PostgreSQL 作为事务数据库。
- 运维团队对云原生组件（K8s、容器化）较熟，对大规模分布式存储维护能力有限。
- 尽量避免强绑定单一云厂商的专有托管产品。

# 备选方案概览

> 详细对比和设计注意事项见：
> `aia-agent-architecture/decisions/0001-vector-db-selection/README.md`

- PostgreSQL + pgvector
- Qdrant
- Milvus

# 决策

- 全局基线方案：**PostgreSQL + pgvector**
  - 用于中小规模数据量（单项目、单团队、多场景整合）的默认实现。
  - 将向量与结构化数据统一落在一套 PostgreSQL 集群中，降低系统复杂度。

- 高规模 / 专用方案：**Qdrant**
  - 当向量条目数量、查询 QPS 或过滤复杂度超过 pgvector 合理范围时，引入 Qdrant 作为专用向量库。
  - 通过抽象的 `VectorStore` 接口支持在不同场景/环境中切换底层实现。

- Milvus 暂不作为默认选型，仅在极大规模、对吞吐/延迟有极致要求的场景中作为评估候选。

# 选择原因（简要）

**PostgreSQL + pgvector**

- 与现有运维栈高度吻合，降低早期落地门槛。
- 适合将代码片段、文档、日志摘要等与结构化元数据存放在一起，方便建模和运维。
- 对于多数中小场景，性能和容量足够，性价比高。

**Qdrant 作为升级路径**

- 专注向量检索，支持 HNSW、过滤、分片等能力，适合数据规模和 QPS 较高的场景。
- 社区活跃，多语言 SDK 和 Docker 部署方式成熟，使用成本可控。

# 架构影响

- 在数据访问层定义统一的 `VectorStore` 接口，至少提供：
  - `upsert_embeddings` / `delete_embeddings`
  - `similarity_search`（可选带过滤条件）
  - `batch` 相关接口
- 为 `VectorStore` 提供两个实现：`PgVectorStore` 与 `QdrantStore`，并通过配置切换。
- 在业务和场景文档中，需显式注明当前使用的实现及未来扩展计划。

# 风险与缓解

- 风险：早期完全依赖 pgvector，可能在数据增长后出现性能瓶颈。
  - 缓解：一开始就通过 `VectorStore` 抽象隔离具体实现；在监控中持续记录 QPS、延迟、索引大小等指标。
- 风险：跨库迁移数据（从 pgvector 到 Qdrant）存在一次性成本。
  - 缓解：保留原始文本和向量生成逻辑，必要时可重建索引；提前设计迁移脚本和数据导出格式。

# 实施与演进计划

- 阶段 1（默认）
  - 新场景默认使用 PostgreSQL + pgvector。
  - 在 `aia-agent-architecture/decisions/0001-vector-db-selection/*.md` 中持续补充各方案实践经验。

- 阶段 2（扩展）
  - 当监控指标或业务需要表明 pgvector 已接近上限时，引入 Qdrant 实例。
  - 在关键服务中切换 `VectorStore` 实现，并通过回归测试验证行为一致。

---

> 相关对比材料：
> - `aia-agent-architecture/decisions/0001-vector-db-selection/README.md`
> - `aia-agent-architecture/decisions/0001-vector-db-selection/postgres-pgvector.md`
> - `aia-agent-architecture/decisions/0001-vector-db-selection/qdrant.md`
> - `aia-agent-architecture/decisions/0001-vector-db-selection/milvus.md`
