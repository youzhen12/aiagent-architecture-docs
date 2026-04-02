---
title: "AIAgent 架构术语表"
status: draft
version: 0.1.0
owner: [文档维护人]
last_updated: 2026-04-02
tags: [glossary, docs]
---

# 术语表（按字母大致排序）

- Agent Orchestrator（Agent 编排器）
  - 含义：负责接收请求、拆解任务、协调多个 Agent/工具执行顺序的组件。
  - 备注：在本项目中通常指自研的协调层，与 LangGraph 等编排框架配合使用。

- LangGraph
  - 含义：基于有向图的 LLM 应用/Agent 编排框架，支持节点、边、状态、checkpoint 等概念。
  - 备注：在多个场景中作为子流程编排工具，与自研 Orchestrator 组合使用。

- Model Gateway（模型网关）
  - 含义：对外提供统一模型调用接口（通常为 OpenAI 兼容协议），对内路由到不同云厂商或自托管模型。
  - 备注：与 `decisions/0003-model-and-inference-service-selection` ADR 对应，是所有模型调用的唯一入口。

- RAG（Retrieval-Augmented Generation，检索增强生成）
  - 含义：先从文档/代码/日志等知识源中检索相关片段，再与用户问题一起输入到模型中生成回答的模式。
  - 备注：对应向量库选型见 `0001-vector-db-selection`。

- Vector DB（向量数据库）
  - 含义：用于存储和检索向量嵌入的数据库，支持相似度搜索和过滤查询。
  - 典型方案：PostgreSQL + pgvector、Qdrant、Milvus 等。

- Observability（可观测性）
  - 含义：通过指标（Metrics）、日志（Logs）和追踪（Tracing）理解系统内部状态的能力。
  - 备注：本项目中通常指 Prometheus + Grafana + 日志平台 + OpenTelemetry 的组合，对应 `0004-observability-and-logging-selection`。

- Prompt Engineering（提示词工程）
  - 含义：围绕提示词设计、模块化和版本管理的系统化实践，而非零散调 prompt。
  - 备注：属于 `0005-engineering-design` 范畴，与 Context/Harness Engineering 一起使用。

- Context Engineering（上下文工程）
  - 含义：围绕「提供给模型的上下文」的采集、组织、压缩和注入策略（包括 RAG、记忆、截断等）。

- Harness Engineering（驯控工程）
  - 含义：通过权限模型、工具运行时、Hook、测试/评估等手段，把模型行为约束在可控边界内的工程方法。
