---
title: "Qdrant 作为向量数据库方案"
status: draft
---

# 描述

Qdrant 是专门面向向量检索场景的开源数据库，支持 HNSW 等 ANN 索引、多租户 Collection、结构化过滤与混合检索（Dense + Sparse）、多向量表示等能力，提供 HTTP/gRPC API 以及多语言 SDK，可通过 Docker/Kubernetes 部署，也有多种云托管形态（Managed / Hybrid / Private Cloud）。

# 优点

- 针对向量检索深度优化：
  - 内置 HNSW 等 ANN 算法，适合高维向量和大规模数据集的近似最近邻搜索。
  - 支持向量相似度 + 结构化过滤条件，以及 Dense/Sparse 混合检索（Hybrid Search），适合结合语义 + 关键词的复杂检索场景。
- 运维与集成选择多样：
  - 提供官方 Docker、Kubernetes 部署方案，自托管起步门槛较低。
  - 云侧提供 Managed / Hybrid / Private Cloud 等模式，可按团队安全与运维能力选择「全托管」「混合托管」「完全自建」。
  - HTTP/gRPC API 设计清晰，多语言 SDK（Python、TypeScript、Go、Java、Rust 等）完善，易于在多语言微服务体系中复用。
- 社区与生态成熟：
  - 文档与教程体系完整，涵盖从基础语义搜索、混合搜索、多向量检索到大规模部署最佳实践。
  - 与 LangChain、LlamaIndex、LangGraph 等主流 LLM 框架有现成集成，可直接复用 RAG 相关组件。
  - 提供 FastEmbed、Edge 版本等配套工具，方便在边缘设备/浏览器等场景做嵌入和检索。
- 可扩展性与可靠性：
  - 支持分片、副本、多节点部署，具备水平扩展能力；
  - Managed/Hybrid 形态提供自动化监控、备份与零停机升级，适合对可用性要求较高的生产环境。

# 缺点

- 引入新的独立服务：
  - 相比「直接用现有关系型数据库」，需要额外运维一个组件，增加部署和监控复杂度。
  - 如果团队没有容器化/集群经验，自托管集群和多区域部署仍有一定学习成本。
- 对传统关系型查询支持有限：
  - 适合做「向量相似度检索 + 过滤」，不适合承载业务交易数据；通常需要与其他数据库（如 PostgreSQL/MySQL）配合使用。
- 产品迭代较快：
  - 新特性（例如 Hybrid Search、Edge/Inference 能力）更新频繁，需要在升级策略、配置兼容性上有良好实践，避免在生产环境踩到行为变更。

# 使用 Qdrant 的开源项目示例

- LangChain & LlamaIndex 官方/社区示例：
  - 官方文档中提供了使用 Qdrant 作为向量存储的 RAG 示例，涵盖文档索引、检索与问答。
- Qdrant 官方示例仓库：
  - 包含多种语言的集成示例（Python、Node 等），展示了基础 CRUD、过滤检索、分片部署等特性。
- 对比本仓库中的项目：
  - `aiguides` 当前没有使用独立向量数据库，而是依赖上游 Gemini 能力和现有数据库，适合单项目场景。
  - `opencode` / `pi-mono` 更偏向「本地上下文 + 会话存储」，未强依赖某一向量库；Qdrant 则适合我们在多项目、多知识源场景下构建统一 RAG 服务。
