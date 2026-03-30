---
title: "使用 LangGraph 进行 Agent 编排"
status: draft
---

# 描述

LangGraph 是基于「有向图」抽象的 LLM 应用/Agent 编排框架，可通过节点（Agent、工具、子流程）和边（控制流）来描述复杂的多 Agent 协作流程，支持循环、分支、检查点（checkpointing）和流式输出，提供 Python 与 TypeScript SDK。

# 优点

- 流程可视化与结构化：
  - 将 Agent 流程显式建模为图结构，便于理解、评审和可视化展示。
  - 复杂流程（如「理解 → 检索 → 规划 → 执行 → 总结」）可以拆成多个节点，职责清晰。
- 内置状态管理和检查点：
  - 支持长对话的状态持久化与恢复，适合需要中断/继续、回滚的场景。
  - 检查点机制有利于在失败时从中间状态恢复，减少重复调用成本。
- 与 LangChain 生态兼容：
  - 可直接复用 LangChain 中已有的工具、RAG 组件与模型封装，减少重复造轮子。
- 多语言支持：
  - Python/TS 双生态支持，有利于在后端与前端/Node 工具中统一编排抽象。

# 缺点

- 额外学习成本：
  - 团队需要理解 Graph/State 模型、节点生命周期、状态传递等概念。
- 对简单流程可能显得「过度设计」：
  - 对于单 Agent 或非常短的线性流程，直接写代码往往更简单直观。
- 框架演进风险：
  - 作为新兴框架，未来 API 与最佳实践可能继续演进，需要关注升级与兼容性。

# 使用 LangGraph 的开源项目示例

- LangChain 官方/示例项目：
  - LangChain 社区提供了多个基于 LangGraph 的多 Agent 示例，如代码助手、数据分析助手等。
- 各类开源 Demo：
  - GitHub 上存在不少基于 LangGraph 搭建的 Agent Playground / RAG Demo，用于展示多 Agent 协作能力。
- 对比本仓库中的项目：
  - `aiguides` 采用路由 + Handler 组织逻辑，没有使用专门编排框架，更适合单产品线场景。
  - `opencode` 和 `pi-mono` 通过自研状态机/事件流实现编排，与 LangGraph 的「图式编排」理念相近，但实现位置（库 vs 框架）不同。
