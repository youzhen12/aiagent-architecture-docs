---
title: "Hermes Agent 场景 MaaS 方案设计"
status: draft
scene_id: scene-hermes-agent
---

# 1. 场景与整体目标

本方案假设：

- Hermes Agent 作为多 Agent 编排与工具调用的运行时平台。  
- 底层大模型与推理服务由云厂商提供（例如阿里云千问/百炼、OpenAI 等），通过统一 Model Gateway 接入。  
- 目标是在云上构建一个支持多场景、多租户的 Agent 平台，为上层业务快速搭建 Hermes 风格的多 Agent 应用。

# 2. 能力地图与责任划分

## 2.1 Hermes Agent 平台负责

- Agent 抽象与运行时：
  - 定义 Agent 类型（Planner、Executor、Reviewer 等）及其生命周期。  
  - 负责 Agent 间协作、任务调度与状态管理。

- 工具注册与执行管道：
  - 统一工具定义（schema + 权限 + 沙盒配置）。  
  - 实现工具执行的输入校验、限流、审计与回滚机制（对齐 `0010` 决策）。

- 记忆与上下文：
  - 为每个会话/项目/用户管理对应的记忆存储（对齐 `memory-engineering.md`）。  
  - 负责在调用模型前组装 prompt，注入记忆与上下文（对齐 `context-engineering.md`）。

- 可观测性与评估：
  - 记录模型调用、工具调用、任务 DAG 与状态，支持调试与评估（对齐 `0004`、`0007`）。

## 2.2 云厂商 MaaS 能力负责

- 大模型与推理服务：
  - 提供对话/工具调用/多模态等基础模型能力。  
- RAG / 知识库服务：
  - 为 Agent 提供企业知识检索能力（对齐 `0008`）。  
- 向量数据库与搜索：
  - 用于项目/长期记忆存储与检索。  
- 安全与合规工具：
  - 提供内容安全检测、隐私检测等 Guardrails 能力。

# 3. 参考架构（抽象）

```text
┌─────────────────────────────────────┐
│           业务应用 / Portal         │
│  - 场景配置（Agent/工具/流程）      │
│  - 业务 UI / API                   │
└─────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────┐
│          Hermes Agent 平台          │
│  - Agent Runtime / Orchestrator    │
│  - Tool Registry & Sandbox         │
│  - Memory & Context Manager        │
│  - Eval & Telemetry                │
└─────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────┐
│           Model Gateway             │
│  - 多厂商模型接入 / 路由            │
│  - 统一鉴权 / 计费 / 调用策略        │
└─────────────────────────────────────┘
                │
                ▼
┌─────────────────────────────────────┐
│          云厂商 MaaS 能力           │
│  - LLM / Chat / Tool Calling       │
│  - RAG / 向量检索 / Search          │
│  - 内容安全 / 日志 / 监控           │
└─────────────────────────────────────┘
```

# 4. 关键设计要点对齐 AIAgent 决策

## 4.1 Agent 编排与 Hermes Runtime（对齐 0002）

- 使用状态机/工作流表达 Hermes 的多 Agent 协作：
  - Planner → Explorer → Coder → Reviewer 等串/并行组合。  
- 对敏感步骤（写入数据库、调用生产 API）插入审批节点（人/审批 Agent）。

## 4.2 Prompt / Context / Memory（对齐 0005）

- Prompt Engineering：
  - 为不同 Agent 定义职责清晰的 prompt 模板。  
- Context Engineering：
  - 为每次调用从项目代码、文档、日志、知识库中构建精简上下文。  
- Memory Engineering：
  - 将 Hermes 的 session/project/user 记忆映射到本项目的记忆层次，使用向量库或文档存储持久化。

## 4.3 工具沙盒与执行控制（对齐 0010）

- 所有工具调用通过统一 Tool Runtime：
  - 输入 schema 校验。  
  - 权限检查（Agent/用户/租户）。  
  - 审计日志（谁在什么时候对哪个资源做了什么）。
- 对 shell/HTTP/数据库工具分别应用不同的沙盒策略：
  - 参考 `shell-and-filesystem-sandbox.md`。  
  - 参考 `http-and-network-sandbox.md`。  
  - 参考 `database-and-internal-service-sandbox.md`。

## 4.4 知识检索（对齐 0008）

- Hermes 中的「项目知识」和「外部知识」可统一由云侧 RAG 服务提供：
  - 小规模、结构清晰内容：使用 grep/全文检索。  
  - 中大型文档库：使用向量检索 + RAG。  
  - 复杂规范/手册：叠加树状索引（Tree Index）。

# 5. 渐进式落地建议

1. 原型阶段：
   - 在单一场景（如代码助手或运维助手）上实现简化版 Hermes 多 Agent 流程。  
   - 重点验证：Agent 角色划分、Tool Runtime、基础记忆功能。

2. 平台化阶段：
   - 抽象出通用的 Agent Runtime / Tool Registry / Memory 模块。  
   - 支持多个场景通过配置/插件方式接入。

3. 多租户与治理阶段：
   - 引入租户隔离、配额管理和跨场景评估指标。  
   - 对 Agent 行为与工具调用进行平台级治理（安全、成本、质量）。
