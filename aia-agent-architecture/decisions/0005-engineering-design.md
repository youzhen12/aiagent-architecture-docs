---
title: "工程设计主线选型（0005）"
status: draft
date: 2026-04-02
decision_id: 0005
owner: [待定]
tags: [adr, prompt-engineering, context-engineering, harness-engineering]
---

# 背景

在 AIAgent 系统中，Prompt 设计、上下文组织和评测方法是工程化的关键部分。本 ADR 作为骨架，用于后续沉淀对 Prompt Engineering、Context Engineering、Harness Engineering 等主题的整体取舍与组合策略。

> 详细主题材料见：`aia-agent-architecture/decisions/0005-engineering-design/` 目录下文档。

# 待补充内容

- 问题与目标：
  - 在不同场景下，如何在「效果」与「成本/可控性」之间平衡？
- 约束条件：
  - 当前模型能力、上下文长度、成本预算等；
- 备选方案：
  - 强依赖 prompt vs 强依赖工具/结构化上下文；
  - 高度自动化 vs 人类在环等；
- 决策与原则：
  - 针对本项目，给出一套默认工程实践原则；
- 实施与演进计划：
  - 如何将这些原则具体落实到各场景与代码结构中。
