---
title: 阿里云 MaaS 场景映射指南
status: draft
version: 0.1.0
owner: [负责人]
last_updated: 2026-04-14
tags: [aliyun, qwen, model-studio, mapping]
---

# 阿里云 MaaS 场景映射指南

> 本文档说明如何将本仓库中的场景架构映射到阿里云 Qwen / 百炼（Model Studio）/ Agent 能力。
> 
> **相关文档**：[README](readme.md) | [产品反馈](aliyun-product-feedback-notes.md) | [场景族谱](scene-landscape.md)

## 0. 阿里云 AI MaaS 能力概览

### 模型层（Qwen 系列 + 第三方模型）

| 类型 | 模型 | 适用场景 |
|------|------|----------|
| 通用大模型 | Qwen3.6-Plus / Qwen3.5-Omni / Qwen3-Max | 通用对话、复杂推理、多轮交互 |
| 代码模型 | Qwen-Coder | 代码生成、理解、工具调用 |
| 多模态模型 | Qwen3-VL、图像/视频生成 | 视觉理解、多媒体处理 |
| Embedding 模型 | Qwen Embedding 系列 | 文本/代码向量化，支持 RAG 与检索 |

### 平台层（百炼 / Model Studio）

| 能力 | 说明 |
|------|------|
| OpenAI 兼容 API | 通过 DashScope 兼容 OpenAI 协议，平滑迁移现有调用 |
| 模型服务 | 训练与微调（SFT/CPT/DPO）、部署、评估 |
| 应用构建 | 单智能体应用、工作流应用（无代码编排）、高代码应用 |
| 知识库与 RAG | 内置知识库，支持文档上传与自动索引 |
| 插件/MCP | 集成外部服务，作为 Agent 工具调用 |

### Agent 开发与生态

- Agent 开发工具链：在线注册托管 MCP 服务、构建多 Agent 工作流
- 模板与 Demo：客服、办公、营销等场景模板

---

## 1. SaaS 平台场景映射

**架构文档**：`scene-saas-platform-agent/`  
**MaaS 案例**：[cases/saas-platform-maas-case.md](cases/saas-platform-maas-case.md)  
**POC 计划**：[maas-poc-plan-saas-platform.md](maas-poc-plan-saas-platform.md)

### 模型与能力映射

| 层级 | 组件 | 阿里云产品 |
|------|------|------------|
| 模型 | 通用分析对话 | Qwen3.6-Plus 或 Qwen3.5-Omni |
| 模型 | 代码/脚本建议 | Qwen-Coder |
| 模型 | 文档向量检索 | Qwen Embedding |
| 平台 | 模型网关 | 百炼 OpenAI 兼容 API |
| 平台 | 文档检索 | 百炼知识库（RAG） |
| 平台 | Agent 编排 | 工作流应用 / Agent 编排 |
| 生态 | API 暴露 | MCP/插件机制 |

### 映射关系

| 架构组件 | 阿里云对应 |
|----------|------------|
| 模型网关 | 百炼 OpenAI 兼容接口 |
| RAG 环节 | 百炼知识库 |
| 多 Agent 编排 | 百炼工作流应用 + Agent 工具链 |

---

## 2. 智能客服场景映射

**架构文档**：`scene-customer-support/`  
**MaaS 案例**：[cases/customer-support-maas-case.md](cases/customer-support-maas-case.md)  
**POC 计划**：[maas-poc-plan-customer-support.md](maas-poc-plan-customer-support.md)

### 模型与能力映射

| 层级 | 组件 | 阿里云产品 |
|------|------|------------|
| 模型 | 中文多轮对话 | Qwen3.5-Plus / Qwen3.5-Omni |
| 模型 | FAQ/文档检索 | Qwen Embedding |
| 平台 | 客服原型 | 单智能体应用 |
| 平台 | 多 Agent 流程 | 工作流应用（Router/FAQ/Task/Escalation） |
| 平台 | 知识管理 | 知识库（RAG）托管 FAQ/文档/工单摘要 |
| 生态 | 系统集成 | 插件/MCP 接入工单/CRM/订单系统 |
| 平台 | 质量评估 | 模型评估模块（自动评分 + 人审） |

---

## 3. 秒杀/大促场景映射

**架构文档**：`scene-flash-sale-platform/`

### 模型与能力映射

| 层级 | 组件 | 阿里云产品 |
|------|------|------------|
| 模型 | 日志/指标分析 | Qwen3.6-Plus / Qwen3.5-Omni |
| 平台 | 模型调用 | 百炼 OpenAI 兼容 API |
| 平台 | 系统对接 | 高代码应用/工作流应用 |
| 平台 | 文档管理 | 知识库管理历史复盘报告 |
| 生态 | 工具封装 | Agent 工具链（监控/日志/风控查询） |

---

## 4. Coding OS / 开发者智能体映射

**架构文档**：`scene-claude-code-assistant/`

### 模型与能力映射

| 层级 | 组件 | 阿里云产品 |
|------|------|------------|
| 模型 | 代码主模型 | Qwen-Coder |
| 模型 | 通用助手 | Qwen3.6-Plus / Qwen3.5-Omni |
| 模型 | 代码/文档索引 | Qwen Embedding |
| 平台 | 模型网关 | 百炼 OpenAI 兼容 API |
| 生态 | 工具暴露 | MCP 支持（文件/Git/CI-CD） |
| 平台 | 云端部署 | 高代码应用部署 Agent runtime |

---

## 5. 跨场景通用使用模式

| 模式 | 说明 |
|------|------|
| P1 | 以 Model Studio 知识库托管业务文档，组合成场景 Agent |
| P2 | 通过 OpenAI 兼容 API 迁移现有 AI 调用，便于统计和 A/B 实验 |
| P3 | 使用 MCP 将业务系统封装成工具，写操作采用「草稿 + 审批」 |
| P4 | 构建 Eval 集与评估任务，系统性衡量效果 |
| P5 | POC 阶段灰度流量验证，再推广到全量业务线 |
