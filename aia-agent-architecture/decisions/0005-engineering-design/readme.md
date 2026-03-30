---
title: "工程设计（提示词 / 上下文 / 驭控）"
status: draft
date: 2026-03-30
decision_id: 0005
owner: [待定]
tags: [ai-agent, architecture, engineering]
---

# 背景

在 AIAgent 体系中，「提示词工程」「上下文工程」「驭控工程」是贯穿各层的三条工程化主线：

- 提示词工程：如何系统化设计与管理 Prompt，使其可复用、可测试、可迭代。
- 上下文工程：如何采集、裁剪、组织与注入上下文，保证模型决策建立在正确的信息之上。
- 驭控工程：如何通过工具、编排与策略在运行时约束与引导模型行为，确保安全、可靠、可解释。

本目录不做具体技术选型，而是对三类工程实践的目标、关注点与常见方案进行拆分说明，作为后续详细设计与实现的导航。

# 范围

- 0005 只关注「如何设计和工程化」三类能力，不关注具体模型/向量库/编排框架的选型（相关见 0001–0004）。
- 具体内容拆分为三个子文件：
  - `prompt-engineering.md`：提示词工程。
  - `context-engineering.md`：上下文工程。
  - `control-engineering.md`：驭控工程。

# 子文档一览

- 提示词工程：`0005-engineering-design/prompt-engineering.md`
- 上下文工程：`0005-engineering-design/context-engineering.md`
- 驭控工程：`0005-engineering-design/control-engineering.md`
