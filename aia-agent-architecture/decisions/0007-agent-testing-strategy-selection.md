---
title: "Agent 测试与评估策略选型（0007）"
status: draft
date: 2026-04-02
decision_id: 0007
owner: [待定]
tags: [adr, testing, eval, agent]
---

# 背景

随着 AIAgent 能力增强，如何验证其行为正确性和安全性变得关键。本 ADR 用于规划在不同场景下如何组合离线 Eval、回归测试、在线指标和人工抽检等手段。

> 详细主题材料见：`aia-agent-architecture/decisions/0007-agent-testing-strategy-selection/README.md`。

# 待补充内容

- 测试目标与范围：功能正确性、安全性、鲁棒性、成本等；
- 主要测试手段：
  - 静态检查、单元测试、集成测试；
  - LLM-as-a-Judge/Eval 集合；
  - 在线指标与业务 KPI；
  - 人工审查与红队测试；
- 在不同场景下的推荐组合；
- 如何将测试策略落地到 CI/CD 与日常开发流程中。
