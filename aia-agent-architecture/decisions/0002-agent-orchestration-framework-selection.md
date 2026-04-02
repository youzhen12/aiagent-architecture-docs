---
title: "Agent 编排框架选型（0002）"
status: accepted
date: 2026-04-02
decision_id: 0002
owner: [待定]
tags: [adr, agent, orchestration]
---

# 背景

AIAgent 需要在多个 Agent、工具调用和外部系统之间进行复杂编排，支持前台对话、后台任务、分支与重试等能力。
本 ADR 记录在多种 Agent 编排框架与自研方案之间的选型结论。

# 问题与目标

- 需要一种可视化/结构化方式描述多 Agent 协作流程，而不是分散在零散脚本中。
- 需要内建状态管理与检查点能力，以支持长对话与错误恢复。
- 需要良好的扩展性与可测试性，便于后续工程落地。

# 约束条件

- 当前以 Python / TypeScript 生态为主。
- 希望复用现有 LLM / RAG 框架（LangChain、LlamaIndex 等）的生态能力。
- 不希望过早引入过于复杂、重型的工作流引擎。

# 备选方案概览

> 详细对比见：`aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/README.md`

- LangGraph
- 自研 Orchestrator
- CrewAI / AutoGen
- Semantic Kernel
- LlamaIndex Agents
- OpenAI Assistants / Workflows

# 决策

- 采用「**自研 Orchestrator + LangGraph 子流程**」混合方案：
  - 自研 Orchestrator：负责统一入口（CLI/IDE/MCP）、任务派发、权限前置控制与会话管理。
  - LangGraph：在具体 Agent 调度链路内表达复杂子流程和状态机（如 fork path / background path / checkpoint 等）。

# 选择原因（简要）

- 自研 Orchestrator 便于深度集成现有工具运行时、权限与观测系统，实现更精细的控制。
- LangGraph 提供成熟的图模型与检查点机制，适合表达复杂 Agent 流程，避免完全从零造轮子。
- 保持框架层与业务逻辑层的清晰分离，有利于测试与长期维护。

# 架构影响

- 引入显式的「编排层」：
  - Orchestrator：面向场景、入口和会话；
  - Flow/Graph：面向具体任务与子流程。
- 需要在代码中定义统一的上下文结构（如 `ConversationState`），在各节点之间传递。
- 对 Observability 的要求提升，需要在 Orchestrator 和 Graph 层都接入日志和 tracing。

# 风险与缓解

- 风险：组合方案的学习成本较高（同时理解自研 Orchestrator 与 LangGraph）。
  - 缓解：提供统一的开发模板与示例项目；在文档中明确「何时使用 Orchestrator，何时使用 Graph」。
- 风险：后续 LangGraph 路线与需求可能存在偏差。
  - 缓解：通过自定义抽象（如 `AgentNode`、`ToolNode`）包装 LangGraph，避免业务强耦合具体框架。

# 实施与演进计划

- 阶段 1：PoC
  - 基于 LangGraph 实现一个端到端示例 Flow。
  - 在 Orchestrator 中集成该 Flow，并打通日志/Tracing。
- 阶段 2：推广
  - 将核心业务场景迁移到混合编排方案上。
  - 形成内部最佳实践与代码模板。

---

> 相关对比材料：
> - `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/README.md`
> - 同目录下各框架的对比文档（langgraph.md、semantic-kernel.md、crewai-autogen.md 等）
