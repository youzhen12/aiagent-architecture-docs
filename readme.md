
## 项目简介

本项目是一个面向开发者的架构设计参考，目标是：

- 总结AIAgent相关技术和架构方案
- 支持不同业务场景下的 AI Agent 解决方案快速搭建与对比


## 文档结构

- `readme.md`：当前文件，文档入口和整体说明
- `aia-agent-architecture/`
  - `overview.md`：AIAgent 总体架构与本目录导航
  - `decisions/`：按编号组织的技术选型与工程决策
    - `000x-some-topic/`：该主题的对比材料（README + 各方案分析）
    - `000x-some-topic.md`：本项目在该主题上的顶层 ADR 决策
  - `templates/`：通用模板
    - `architecture-overview-template.md`：通用架构总览模板
    - `scene-architecture-overview-template.md`：`scene-*/architecture-overview.md` 场景模板
    - `maas-solution-design-template.md`：MaaS 解决方案设计模板
- `scene-*/`：具体业务/使用场景下的架构文档
  - 每个子目录代表一个场景（例如：本地 IDE 助手、代码审查 Agent、知识库问答系统等）
  - 建议在每个场景目录中至少包含：
    - `ai-application-overview.md`：场景与需求说明
    - `architecture-overview.md`：基于模板的场景级架构总览
    - `maas-solution-design.md`（如适用）：端到端解决方案设计

### 现有 `decisions/` 子目录说明

当前已存在的几个决策目录分别用于：

- `0001-vector-db-selection/`：向量数据库选型，例如 Postgres + pgvector、Qdrant、Milvus 等，在这里对比检索性能、运维复杂度、成本与生态。
- `0002-agent-orchestration-framework-selection/`：智能体编排框架选型，如 LangGraph、Semantic Kernel、LlamaIndex Agents、DeerFlow / 自研 Orchestrator、CrewAI / AutoGen、OpenAI Assistants / Workflows 等，用于比较易用性、可观测性、可扩展性和与现有系统的集成方式。
- `0003-model-and-inference-service-selection/`：模型与推理服务选型，涵盖直连厂商 API、自建 / 第三方推理网关等，主要关注成本、性能、可用性、多模型切换能力等维度。
- `0004-observability-and-logging-selection/`：可观测性与日志方案选型，例如仅日志、Prometheus + Grafana、自建或 SaaS 观测平台，用于评估在不同规模下的监控告警、日志分析与运维体验。
- `0005-engineering-design/`：工程设计主线，聚焦提示词工程、上下文工程、驭控工程（Harness Engineering）等工程性主题，用于沉淀「如何在真实项目中驱动和约束模型」的共性方法与实践。
- `0006-foundation-models-selection/`：大模型厂商与版本选型，每个主流厂商一个子文档，对比 OpenAI、Anthropic Claude、Google Gemini、Meta Llama、Mistral、Qwen 等模型家族及其主要版本（能力、成本、多模态、上下文长度等），为各 `scene-*/architecture-overview.md` 中第 4 章的模型选型提供参考。
- `0007-agent-testing-strategy-selection/`：Agent 测试与评估方案选型，聚焦如何设计 Eval 集、回归测试、在线指标、人审抽检、高危行为测试等，帮助在不同场景下选择合适的测试策略组合。

此外，每个编号还对应一份「顶层 ADR 文档」：

- `aia-agent-architecture/decisions/0001-vector-db-selection.md`
- `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection.md`
- `aia-agent-architecture/decisions/0003-model-and-inference-service-selection.md`
- `aia-agent-architecture/decisions/0004-observability-and-logging-selection.md`
- `aia-agent-architecture/decisions/0005-engineering-design.md`
- `aia-agent-architecture/decisions/0006-foundation-models-selection.md`
- `aia-agent-architecture/decisions/0007-agent-testing-strategy-selection.md`

约定：

- `000x-*/README.md` 与子文档：聚焦「选型维度与各方案对比」，可长期演进；
- `000x-*.md`：作为本项目在该主题上的「当前决策快照」，重点写清「背景、备选、结论、影响与演进计划」。

## 使用建议

- 新增技术调研或选型时：
  - 在 `aia-agent-architecture/decisions/` 下新建一个目录，并补充或创建对应的 `000x-*.md` 顶层 ADR；
- 针对特定落地场景做设计时：
  - 新增一个场景目录，复制 `aia-agent-architecture/templates/scene-architecture-overview-template.md` 为该场景的 `architecture-overview.md`；
- 当项目演进或技术更替时：
  - 优先更新对应的 `decisions/000x-*.md` 决策文档及相关场景架构文档，并在文档中明确「变更原因」和「影响范围」。

### 推荐阅读路径

- 对 AIAgent 概念不熟：
  - 从 `research/`（如有）与各场景的 `ai-application-overview.md` 入手；
- 想了解整体架构：
  - 阅读 `aia-agent-architecture/overview.md` 与关键 `decisions/000x-*.md`；
- 为某个场景做落地：
  - 从该场景目录下的 `ai-application-overview.md` → `architecture-overview.md` → `maas-solution-design.md`（如有），并结合 `decisions/000x-*.md` 调整选型。

## Frontmatter 建议规范

- 通用字段：
  - `title`: 文档标题，简要准确；
  - `status`: `draft` / `reviewing` / `accepted` / `deprecated` / `retired`；
  - `version`: 语义化版本号，如 `0.1.0`；
  - `owner`: 负责维护该文档或领域的角色/人；
  - `last_updated`: `YYYY-MM-DD`；
  - `scene_id`（如适用）：`scene-xxx`，用于场景文档；
  - `tags`: 简短标签，如 `[adr, scene, template, research]`。

- 对 ADR 文档（`decisions/000x-*.md`）特别建议：
  - `status` 只使用 `draft` / `accepted` / `deprecated` / `superseded`；
  - 若被新决策替代，在文内显式注明「superseded by 000y-...」。

## 写作 / 贡献简要指南

- 新增一个技术选型主题：
  - 在 `aia-agent-architecture/decisions/` 下新建目录 `000x-some-topic/`，添加 `README.md` 与若干方案对比文档；
  - 同时新增 `aia-agent-architecture/decisions/000x-some-topic.md`，按 ADR 模式写清本项目当前决策。

- 新增一个业务场景：
  - 在 `docs/` 下新建 `scene-something/` 目录；
  - 复制模板：
    - `aia-agent-architecture/templates/scene-architecture-overview-template.md` → `scene-something/architecture-overview.md`
    - 按需要增加 `ai-application-overview.md` 与 `maas-solution-design.md`；
  - 在 `architecture-overview.md` 的核心技术选型章节中引用相关 `decisions/000x-*.md`。

- 修改已有决策：
  - 优先更新对应的 `000x-*.md` 顶层 ADR，并在其中说明变更原因与影响；
  - 如与多个场景相关，检查对应 `scene-*/architecture-overview.md` 是否需要同步更新。

- 不确定术语时：
  - 先查阅 `docs/glossary.md`，确保中英文使用和含义与现有文档保持一致。

欢迎大家一起维护、更新
