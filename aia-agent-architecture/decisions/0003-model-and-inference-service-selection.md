---
title: "模型与推理服务选型（0003）"
status: accepted
date: 2026-04-02
decision_id: 0003
owner: [待定]
tags: [adr, llm, inference, gateway]
---

# 背景

AIAgent 系统依赖大语言模型（LLM）及相关 Embedding 模型。为便于多模型管理、成本控制和厂商切换，需要明确「模型从哪里来、如何调用」的总体方案。

# 问题与目标

- 支持多种模型类型：Chat / Completion / Embedding 等。
- 支持多模型源：不同云厂商、本地/自托管模型。
- 为上层提供统一接口，降低与具体厂商的耦合。

# 约束条件

- 当前主要消费 OpenAI 兼容协议；
- 未来希望支持自托管推理（vLLM/Ollama 等）；
- 需要为权限控制、配额管理和审计预留空间。

# 备选方案概览

> 详细对比见：`aia-agent-architecture/decisions/0003-model-and-inference-service-selection/README.md`

- 直接调用各厂商 API（direct-vendor-api）
- 自建统一推理网关（self-hosted-gateway）
- 使用第三方统一网关（third-party-gateway）

# 决策

- 采用方案：**自建 OpenAI 兼容统一推理网关（Model Gateway）**。
- 上层一律通过内部 `Model Gateway` 调用模型，不直接依赖外部厂商 SDK。

# 选择原因（简要）

- 保持灵活性：便于在网关内部切换/混合使用不同厂商和自托管模型。
- 降低耦合：应用层只感知统一协议（OpenAI 兼容），更易测试与演进。
- 统一治理：可在网关层集中做鉴权、配额、日志、Tracing 等能力。

# 架构影响

- 新增 `Model Gateway` 组件，职责包括：
  - 多模型路由；
  - 调用重试与超时；
  - 基础的配额和限流；
  - 记录调用日志与 tracing span。
- Agent 与工具只依赖内部 `ModelClient`，由其访问 Gateway。

# 风险与缓解

- 风险：网关成为性能瓶颈或单点故障。
  - 缓解：从简单实现起步，尽早做压测；网关无状态化以支持水平扩展；保留「应急直连厂商 API」的旁路配置。
- 风险：早期对各厂商特性的兼容不完善。
  - 缓解：优先实现通用能力，对厂商特性通过「扩展字段 + 能力探测」方式逐步支持。

# 实施与演进计划

- 阶段 1：基础网关
  - 实现 chat/completions、embeddings 等核心接口；
  - 接入至少一家云厂商；
  - 打通日志与错误监控。
- 阶段 2：多模型与自托管
  - 增加多模型路由配置；
  - 接入自托管模型，验证延迟与成本收益；
  - 逐步增强配额、AB 测试等高级能力。

---

> 相关对比材料：
> - `aia-agent-architecture/decisions/0003-model-and-inference-service-selection/README.md`
> - 同目录下各实现方式的评估文档（direct-vendor-api.md、self-hosted-gateway.md、third-party-gateway.md）
