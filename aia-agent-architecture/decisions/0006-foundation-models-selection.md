---
title: "基础模型选型（0006）"
status: draft
date: 2026-04-02
decision_id: 0006
owner: [待定]
tags: [adr, foundation-models, provider-selection]
---

# 背景

大模型供应商和版本众多，本 ADR 用于总结在 OpenAI、Anthropic Claude、Google Gemini、Meta Llama、Mistral、Qwen 等主流模型家族之间的选型原则与默认组合。

> 详细对比材料见：`aia-agent-architecture/decisions/0006-foundation-models-selection/` 目录下各厂商文档。

# 待补充内容

- 场景划分：交互主线、后台批处理、低成本场景等；
- 评估维度：能力、成本、上下文长度、多模态支持、企业支持等；
- 默认模型组合与备选模型；
- 与 0003 模型网关决策之间的关系；
- 演进策略（如何替换或叠加新模型）。
