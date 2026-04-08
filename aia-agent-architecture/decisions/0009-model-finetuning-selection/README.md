---
title: "0009 大模型微调选型 - 对比材料"
status: draft
---

# 本目录用途

本目录补充 `0009-model-finetuning-selection.md` 中的顶层决策，提供更细粒度的对比与实践材料。例如：

- 不同微调路线的技术细节与工程成本对比。
- 厂商托管微调服务的能力对比（如 OpenAI、Qwen 等）。
- 开源框架（如 Hugging Face PEFT、DeepSpeed、各种 LoRA/QLoRA 实现）的实践经验。

> 顶层决策请参见：`aia-agent-architecture/decisions/0009-model-finetuning-selection.md`。

# 计划中的子文档

- `routes-overview.md`：
  - 全量微调 vs PEFT vs 厂商托管微调的技术与成本对比。
- `vendor-openai.md`：
  - OpenAI 微调相关产品形态与注意事项。
- `vendor-qwen-aliyun.md`：
  - 以阿里云千问/百炼为例，梳理厂商微调与知识库能力的组合方式。
- `peft-and-lora.md`：
  - 围绕 LoRA/QLoRA、Prefix Tuning 等方法，总结常见配置、坑点与调优经验。

后续可以根据实际项目进展，逐步将这些文件补充完整，并在顶层 ADR 中引用关键结论。
