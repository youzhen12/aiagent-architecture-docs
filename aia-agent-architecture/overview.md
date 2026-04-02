---
title: "AIAgent 总体架构概览"
status: draft
version: 0.1.0
owner: [待定]
last_updated: 2026-04-02
---

# 1. 文档目的

- 作为整个 `docs/aia-agent-architecture/` 目录的「导航图」和总览说明；
- 串联各类决策（`decisions/000x-*`）、模板（`templates/`）与场景文档（`scene-*/`）；
- 提供从「全局架构」到「具体场景」的阅读入口。

# 2. 目录结构约定

- `decisions/`：按编号组织的技术选型与工程决策；
- `overview.md`：本文件，整体架构与目录导航；
- `templates/`：通用模板（架构文档、解决方案设计等）；
- `research/`（预留）：调研类长文，可根据需要补充。

# 3. 决策文档组织方式

- 目录结构：
  - `decisions/000x-some-topic/`：
    - `README.md`：该主题的选型维度与注意事项；
    - 其它 `*.md`：针对单个方案的调研与评估（例如 `qdrant.md`、`langgraph.md` 等）。
  - `decisions/000x-some-topic.md`：顶层 ADR，记录本项目在该主题上的当前决策快照。

- 使用建议：
  - 想了解「有哪些可选方案、怎么对比」：先读目录下的 `README.md` 和子文档；
  - 想知道「本项目当前选了什么、为什么」：读对应的 `000x-*.md` 顶层 ADR。

# 4. 与场景文档的关系

- 每个具体场景（`docs/scene-*/architecture-overview.md`）在「核心技术选型」章节中：
  - 给出该场景下的实际选型结论；
  - 同时引用 `decisions/000x-*` 作为通用对比材料。

- 当场景与全局 ADR 不一致时，需在场景文档中说明「偏离原因」和「风险评估」。
