---
title: 初学者练手 AIAgent 场景总体架构设计
status: draft
version: 0.1.0
owner: [负责人]
last_updated: YYYY-MM-DD
scene_id: scene-beginner-playground
---

# 1. 背景与目标

- 场景简介：
  - 面向刚接触大模型和 AIAgent 的初学者，目标是理解基础概念（Prompt、上下文、工具调用、简单编排），而不是立即构建生产级系统。
- 业务问题：
  - 学习资料分散、demo 多但缺乏系统性架构视角；
  - 很多教程一开始就引入大量复杂组件，导致初学者在基础尚未理解时就被基础设施淹没。
- AIAgent 目标：
  - 提供一个简洁的练手环境，支持：
    - 简单问答和 RAG；
    - 一两个工具调用（如读写本地文件、调用 Web API）；
    - 简单多步工作流（如「提问 → 检索 → 生成答案」）。
- 文档目标：
  - 明确「最小可行」架构：哪些组件是必须的，哪些可以留到以后；
  - 帮助初学者在理解基础概念的同时，为未来扩展到生产场景预留空间。

# 2. 业务与使用场景

- 主要用户与角色：
  - 单个初学者或小组学习者；
- 关键使用场景（示例）：
  1. 学习工具调用：写一个简单代码，让 LLM 调用「天气查询 API」，理解函数调用模式；
  2. 学习 RAG：对一个小型 Markdown 文库实现检索 + 回答；
  3. 学习多步工作流：用 while 循环或 LangGraph 实现「分步计划和执行」。

# 3. 整体架构概览

- 一句话描述整体架构风格：
  - 「单进程/少组件 + 直连一个模型 + 简单存储（文件/SQLite）+ 尽量少的基础设施」。

# 4. 核心技术选型与原因

## 4.1 模型与推理服务

- 选型结论：
  - 使用一个主力云模型（如 OpenAI 或 Claude）或一个本地开源模型（Llama/Qwen）即可；
  - 不在早期引入多模型路由、网关等复杂概念。
- 选择原因：
  - 降低概念负担，让初学者将注意力集中在 Prompt、上下文和工具调用。

## 4.2 向量数据库与检索

- 选型结论：
  - 初期不使用专门向量库，使用内存数据结构或 SQLite 存储少量嵌入即可；
  - 当对 RAG 理解加深后，再尝试 pgvector 或单节点 Qdrant。

## 4.3 编排与工作流

- 选型结论：
  - 起步：直接写 while 循环 + 函数，让模型决定是否继续或调用某个工具；
  - 进阶：使用 LangGraph 描述简单 2–3 节点流程，理解图式编排概念。

## 4.4 观测与调试

- 选型结论：
  - 完全可以使用 `logs-only` 方案：
    - 打印 prompt、上下文和输出到控制台或日志文件；
  - 可选：统计调用次数和错误数，理解 metrics 作用。

## 4.5 工程设计

- 提示词工程：
  - 鼓励初学者为不同任务保存不同版本的 Prompt，并观察输出差异；
- 上下文工程：
  - 从最简单的「把所有信息放在 Prompt 里」过渡到「检索 + 上下文注入」，理解两者差别；
- 驭控工程：
  - 强调「不要让 Agent 直接执行任何破坏性操作」，所有命令类输出都先打印给人确认。

# 5. 核心组件与流程（简略）

- 组件：
  - CLI/简单 Web 界面；
  - 单个应用进程（包含 Agent 逻辑、工具和模型调用）；
  - 本地文件/SQLite 存储数据与配置。
- 流程示例：
  - 用户输入 → Agent 解析意图 → 若需要检索则访问简易索引 → 调用模型生成回答 → 打印/展示结果并记录日志。

# 6. 关联文档

- `aia-agent-architecture/overview.md`
- `aia-agent-architecture/decisions/0001-vector-db-selection.md`
- `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection.md`
- `aia-agent-architecture/decisions/0003-model-and-inference-service-selection.md`
- `aia-agent-architecture/decisions/0004-observability-and-logging-selection.md`
- `aia-agent-architecture/decisions/0005-engineering-design.md`
