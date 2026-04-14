---
title: "Hermes Agent 源码架构总览"
status: draft
scene_id: scene-hermes-agent
---

# 1. 背景与目标

Hermes Agent 是 Nous Research 推出的一个自托管、多场景智能体系统，本场景结合官方仓库结构与社区文章《深度解析 Hermes Agent：从源代码到架构设计》，从工程视角总结其关键设计，并对比本项目的相关决策（`0002`、`0005`、`0008`、`0010` 等）。

本场景关注以下问题：

- Hermes 如何抽象「Agent」「工具」「任务」？
- 多 Agent 协作链路如何表达与执行？
- 记忆与上下文是如何组织与注入的？
- 工具调用与执行沙盒如何设计？

# 2. 业务与使用场景

典型使用方式包括：

- 作为多 Agent 协作平台：
  - 面向复杂任务：由规划 Agent 拆分子任务，再由不同执行 Agent 完成。  
- 作为工具统一调用层：
  - 将 CLI、脚本、HTTP API、内部服务封装为工具，由 Agent 通过声明式接口调用。  
- 作为「Agent 应用」的运行时：
  - 用户只需定义 Agent、工具和流程配置，Hermes 负责调度与状态管理。

# 3. 整体架构概览

从源码结构和文章描述看，Hermes Agent 大致包含以下层次：

- Agent 执行内核（`agent/`）：
  - 负责对话管理、任务调度、prompt 构建与会话压缩。  
  - 通过 LLM Provider 抽象接入不同大模型（OpenAI、Nous Portal 等）。

- CLI 交互层（`hermes_cli/`）：
  - 提供 `hermes setup`、`hermes model`、`hermes gateway` 等命令，用于本地配置和运维。  
  - 允许运行本地 Hermes Agent 并进行调试。

- 工具与执行后端（`tools/`）：
  - 工具注册与验证：将外部工具统一注册、校验接口一致性与安全性。  
  - 执行环境：支持本地、Docker、SSH 等多种执行后端，扩展应用场景。  
  - 定制扩展：用户可按需开发并注册自定义工具。

- 多平台消息网关（`gateway/`）：
  - 负责将 Hermes Agent 接入 Telegram、Slack、WhatsApp 等平台，进行协议转换与消息格式兼容处理。

- 定时任务调度（`cron/`）：
  - 支持周期性任务（报表、巡检、清理等）的自动执行，使 Hermes 成为「长期运行、自动化」的智能体。

- 可选技能与集成环境（`optional-skills/`、`environments/`）：
  - 提供技能样例和与 RL 环境等集成的示例，展示如何将技能和环境接入 Hermes。

这些层次与 AIAgent 的决策体系对应：

- `0002`：Agent 编排框架的功能点。  
- `0005`：Prompt / Context / Memory / Harness / Skill 工程的落地。  
- `0008`：记忆与知识索引（FTS5 + 向量检索）的实践。  
- `0010`：工具沙盒和执行控制（本地/Docker/SSH 的受控调用）。

在 AIAgent 的决策体系中，这对应：

- `0002`：Agent 编排框架的功能点。  
- `0005`：Prompt / Context / Memory / Harness 工程的落地。  
- `0010`：工具沙盒和执行控制。

# 4. 核心技术选型与原因

> 本节从通用多 Agent 框架模式出发，抽象 Hermes Agent 的可能选型和设计理由。

## 4.1 模型与推理服务

- 选型结论（抽象）：
  - 支持插拔式接入多家大模型（如 OpenAI、Anthropic、Qwen 等），通过统一接口封装。  
- 选择原因：
  - 多 Agent 场景可能需要不同模型承担不同角色（例如：推理型 vs 工具调用型）。  
  - 通过抽象层屏蔽不同厂商 API 的差异，便于切换和组合。
- 参考决策文档：
  - `0003-model-and-inference-service-selection.md`  
  - `0006-foundation-models-selection.md`

## 4.2 Agent 编排框架

- 选型结论（抽象）：
  - 使用显式的工作流/状态机来表达多 Agent 协作，而不是完全依赖模型在隐式对话中自行规划。  
- 选择原因：
  - 复杂任务需要可视化、可调试的执行轨迹。  
  - 便于为关键节点插入审批、重试、回滚等策略。  
- 对比：
  - 类似 LangGraph、CrewAI 的做法，Hermes 更像一个「专注于 Agent 协作」的运行时，而非单轮调用封装器。  
- 参考决策文档：
  - `0002-agent-orchestration-framework-selection.md`

## 4.3 记忆系统与技能体系

- 记忆系统：
  - Hermes 使用 FTS5 索引 + 向量存储结合的方式管理记忆：
    - FTS5 用于高效全文索引对话内容与执行结果。  
    - 向量存储用于语义检索，与当前任务语义对齐。  
  - 记忆既包含「对话历史」，也包含「执行任务的结果」，并在新任务中用于提升决策效果。

- 技能体系（Skills）：
  - Hermes 会自动生成和执行技能：
    - 通过对话和任务执行，总结出可复用的步骤或策略。  
    - 将这些技能保存为结构化的 `SKILL.md` 文档（类似本仓库的 Skill 设计）。
  - 下次遇到类似任务时，系统会自动检索并调用这些技能，提高效率。

- 设计原因：
  - 将「一次性的执行经验」沉淀为结构化知识，形成成长型 Agent。  
  - 将复杂的多步任务封装为可重放的技能，提高可维护性与可观测性。  

- 对本项目的映射：
  - 记忆工程：呼应 `memory-engineering.md` 中的多层记忆设计，可借鉴 FTS5 + 向量检索组合。  
  - Skill 工程：与 `skills/` 目录和 prompt-native skill 设计呼应，可考虑用 `SKILL.md` 作为 skill 的一等描述物。

## 4.4 工具沙盒与执行控制

- 选型结论（抽象）：
  - 所有工具调用经由统一的「工具执行管道」：
    - 输入校验（schema + 自定义验证）。  
    - 权限检查（基于 Agent/用户/租户）。  
    - 审计与限流。  
    - 实际执行（shell / HTTP / DB / 内部服务）。
- 选择原因：
  - 避免模型直接构造并执行高危命令。  
  - 便于统一管理权限和审计策略。  
- 参考决策文档：
  - `0010-tool-sandbox-and-execution-control.md`

# 5. 核心组件与流程（抽象示例）

以下是一个典型的 Hermes Agent 多 Agent 协作流程（抽象化示例）：

1. 用户提交复杂任务请求（如「为这个项目补全测试并修复报错」）。  
2. 入口 Agent 将请求转交给 Planner Agent：
   - Planner 分析需求，拆分为若干子任务，并为每个子任务指定目标 Agent 类型（Explorer / Coder / Reviewer 等）。
3. 调度层根据规划生成任务 DAG，并按拓扑顺序执行：
   - Explorer Agent 使用只读工具（grep、文件浏览）收集上下文。  
   - Coder Agent 使用写工具（编辑文件、运行测试）进行修改。  
   - Reviewer Agent 使用只读工具 + 测试工具进行验证，并给出 verdict。
4. 在任务执行过程中：
   - 记忆组件持续记录有价值的状态和决策。  
   - 工具执行管道对每次执行进行校验、限流与审计。  
   - 监控系统记录模型调用与任务进度，以便调试与评估。
5. 任务完成后：
   - 汇总结果与日志，并更新项目记忆（例如：记录新约定或坑点）。

# 6. 数据与存储设计（抽象）

- 任务与会话存储：
  - 存储任务 DAG、状态、每步执行结果和日志。  
- 记忆存储：
  - session memory、project memory、user/team memory。  
- 可观测性数据：
  - 结构化日志、指标与 Trace，用于分析 Agent 行为和成本。

# 7. 非功能性要求

- 性能与延迟：
  - 对多 Agent 工作流做并行化执行和缓存优化（如 prompt cache）。
- 可用性与容错：
  - 对关键节点设计重试、fallback Agent 或人工兜底。  
- 安全与合规：
  - 严格控制工具能力、访问边界与数据持久化策略。  
- 成本与资源：
  - 通过决策机制选择不同模型和调用模式（高/低成本模型切换）。

# 8. 对本项目的启发

结合 Hermes Agent 的抽象架构，对 AIAgent 的启发包括：

1. 明确「Agent 平台」与「具体场景」的边界：
   - 平台提供 Agent/工具/编排/记忆/沙盒等通用能力。  
   - 场景只定义具体 Agent 类型、工具组合和流程配置。

2. 将记忆工程与上下文工程解耦：
   - 明确记忆层次，以便在不同场景中重用。  
   - 引入类似 `memdir` 的项目记忆目录结构。

3. 工具执行必须通过统一管道：
   - 参考 Hermes/Pi-Mono 的 Tool Runtime 设计，将所有工具调用集中管理。  

4. 多 Agent 协作要有明确的角色与职责：
   - 参考 Claude Code 的 Explore/Plan/Verification 模式，避免「万能 Agent」。

后续可根据需要在本场景目录下增加更细粒度的模块解读（例如 Hermes 中具体的任务系统、权限模型等）。
