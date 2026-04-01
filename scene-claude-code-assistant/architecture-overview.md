---
title: Claude Code 启发的本地多 Agent Coding OS 架构设计
status: draft
version: 0.1.0
owner: [负责人]
last_updated: 2026-04-01
scene_id: scene-claude-code-assistant
---

# 1. 背景与目标

- 场景简介：
  - 本场景面向「本地或近端开发环境中的多 Agent Coding Operating System」，以 Claude Code 源码架构为主要参考对象，构建一个可扩展、可治理、可观测的 IDE / 代码工作区智能操作系统，而不仅仅是一个「会写代码的聊天机器人」。
- 业务问题：
  - 现有许多 Coding Agent 只是在 LLM 外面包一层 CLI/TUI，缺少系统化的 Prompt 架构、工具治理与权限模型，导致：
    - 行为不稳定：时好时坏，容易乱改代码或生成无关修改；
    - 安全性不足：对高危操作缺乏权限与确认机制，容易误删文件、乱跑脚本；
    - 难以扩展：Skill / Plugin / MCP 等扩展能力对模型不可见，生态难以系统化沉淀；
    - 缺乏可观测性：难以对 Agent 行为做审计、调试和经验复用。
- AIAgent 目标：
  - 提供一套「以 Agent 为中心的本地开发操作系统」能力，包括：
    - 面向代码库的多 Agent 协作（探索、规划、实现、验证分工）；
    - Prompt 架构化与缓存边界设计，减少 token 成本与不必要的上下文污染；
    - 工具调用治理（Tool Runtime + Permission + Hook Policy）；
    - Skill / Plugin / MCP 等生态化扩展，并让模型「感知」这些能力；
    - 对 Agent 生命周期、工具调用链路、权限决策做可观测与审计。
- 文档目标：
  - 将《Claude Code 源码深度研究报告》中总结的核心设计原则转化为一个可复用场景架构；
  - 明确在自研 Coding OS 时，需要在哪些层面「复制 Claude Code 的系统性优势」，而不是只抄一段 system prompt；
  - 为后续实现团队提供组件拆分、技术选型与演进路线参考。

# 2. 业务与使用场景

- 主要用户与角色：
  - 本地开发者：在 IDE / Terminal 中使用 Coding OS 进行日常开发、重构、调试与文档编写；
  - 团队 Tech Lead / 架构师：定义团队级 Skill / Plugin / Hook / 权限策略，将团队工程规范「制度化」注入到 Coding OS 中；
  - 平台/工具团队：负责维护模型网关、工具执行环境、权限模型及观测系统。

- 关键使用场景（示例）：
  1. 代码探索（Explore Agent）：
     - 开发者在代码库中想快速理解一个复杂模块时，触发 Explore Agent；
     - Agent 只能读取代码（Glob/Grep/FileRead 等），不能改动任何文件；
     - 输出结构化的代码地图、调用关系和关键信息路径。
  2. 设计与规划（Plan Agent）：
     - 在做较大重构或新功能时，先由 Plan Agent 读取需求与现有代码，输出实现方案和关键文件列表；
     - 不直接改代码，只输出 step-by-step 方案与风险点。
  3. 实现与修改（General Purpose / Executor Agent）：
     - 基于 Plan 结果，执行具体修改；
     - 通过 FileEdit/FileWrite 等工具进行变更，但所有高危操作必须经过权限模型与 Hook 审查。
  4. 验证与对抗测试（Verification Agent）：
     - 修改完成后，触发 Verification Agent；
     - 自动运行 lint/test/build/端到端验证等命令，用「试图打破实现」的心态检查变更质量；
     - 输出 PASS / FAIL / PARTIAL verdict 与具体命令/输出记录。
  5. Skill / Plugin 工作流复用：
     - 团队将常见工作流（如「新增后端接口 + 写测试 + 更新文档」）封装为 Skill / Plugin；
     - 开发者通过 slash 命令或菜单调用，Agent 读取 Skill 定义和 Frontmatter，按规范流程执行。

- 典型业务流程：
  - 流程 A：复杂需求从探索到验证的全链路：
    - 开发者在 IDE 触发「实现需求 X」→ Coordinator 根据上下文触发 Explore Agent（只读）→ Plan Agent 输出方案 → General Purpose Agent 执行实现 → Verification Agent 做对抗验证 → 输出总结报告和下一步建议。
  - 流程 B：团队级工程规范落地：
    - 平台团队编写 Skill / Plugin / Hook / Permission 规则 → 部署到 Coding OS 生态 → 模型通过 Agent 列表、Skill 列表、MCP instructions 等「感知」这些扩展能力 → 日常开发中自动套用这些规范。

# 3. 整体架构概览

- 一句话描述整体架构风格：
  - 「本地/近端 Agent Operating System：多入口（CLI/IDE/MCP），以 AgentTool/SkillTool 为核心的工具层 + Prompt Assembly 引擎 + 权限模型与 Hook 政策层 + Async/Background Agent 生命周期管理 + 统一模型网关与观测系统」。

- 架构图占位：
  - [ ] 逻辑架构图：从用户入口（IDE/CLI/MCP）→ Coordinator → AgentTool / SkillTool → 各类 Agents → 工具层（文件/Git/CI/MCP）→ 模型网关 → 结果返回的整体链路。
  - [ ] 部署架构图：
    - 本地单机模式：所有组件运行在开发者机器上；
    - 远程工作区模式：Coordinator/工具服务在远程容器，前端通过 SDK / MCP 接入；
    - 混合模式：模型网关、观测平台在云端，本地只保留薄客户端与工具执行 Sandbox。

- 核心组件清单：
  - EntryPoints：CLI / TUI / IDE 插件 / MCP entrypoints，负责将用户交互统一接入 Coordinator；
  - Coordinator：
    - 负责不同入口之间的路由、会话上下文管理与多 Agent 协调（类似 Claude Code 的 `src/coordinator/`）；
    - 管理前台/后台 Agent 生命周期与任务队列；
  - Prompt Assembly 引擎：
    - 负责按「静态前缀 + 动态后缀」拼装 system prompt；
    - 管理 SYSTEM_PROMPT_DYNAMIC_BOUNDARY 及 prompt cache 相关策略；
  - AgentTool / SkillTool：
    - AgentTool：根据 Agent 定义与调用参数，选择内置 Agent 或 fork path，构建子 Agent 的运行时上下文；
    - SkillTool：加载 Skill 包（markdown + frontmatter），注入 allowed-tools、runtime 变量与上下文；
  - Built-in Agents：Explore / Plan / Verification / General Purpose 等内置 Agent 集合；
  - Tool Runtime 层：
    - 文件工具（FileRead/FileEdit/FileWrite）、搜索工具（Glob/Grep）、任务工具（TodoWrite/TaskCreate）、Bash 等；
    - 执行路径统一经过 toolExecution 管道（input 校验 + PreToolUse Hooks + Permission 决策 + PostToolUse Hooks）；
  - 权限与 Hook 层：
    - Permission Model：定义 allow/ask/deny 及高危操作分类（破坏性、难恢复、共享状态、对外可见、上传第三方等）；
    - Hooks：PreToolUse / PostToolUse / Failure Hooks，作为 runtime policy layer；
  - Model Gateway：统一代理对各厂商 LLM 的调用，负责鉴权、限流、日志、OTel 上报；
  - Observability & Telemetry：记录 Agent transcript、tool use、hook decision、permission 结果与成本数据。

# 4. 核心技术选型与原因（必填）

> 本节给出「本场景的实际选型结论与原因」，
> `aia-agent-architecture/decisions/` 下的文档仅作为通用技术对比材料。

## 4.1 模型与推理服务

- 选型结论：
  - 使用自建或托管的 OpenAI 兼容模型网关（参考 0003），支持以下模型组合：
    - 交互主线：OpenAI GPT-5.x / Anthropic Claude Sonnet 4.x（依据团队实际可用资源选择）；
    - 后台任务与低成本场景：如 GPT-5.3 Instant、Qwen3.5-Plus 等性价比较高的模型；
    - 对于离线批量分析，可以挂载本地或远程开源模型（如 Llama 3.x、Qwen3.x）。
- 选择原因：
  - 该场景需要支持多模型混用：
    - 代码理解与规划偏向高能力、大上下文模型；
    - 简单改动、日志分析等可使用小模型降成本；
  - OpenAI 兼容网关便于与 Claude Code 类似的工具协议集成（统一 Chat/Tools 接口），利于 Prompt / ToolUseContext 抽象。
- 考虑过但暂不推荐的方案：
  - 直接绑死单一厂商 API：短期上手快，但后续在权限治理、观测与多模型切换上受限明显；
  - 完全自建推理集群：对大多数团队而言维护成本过高，适合作为后期演进方向。
- 相关决策文档：
  - `aia-agent-architecture/decisions/0003-model-and-inference-service-selection/`
  - `aia-agent-architecture/decisions/0006-foundation-models-selection/`

## 4.2 向量数据库与检索方案

- 选型结论：
  - 代码与文档索引：
    - 小团队/单仓库场景：优先使用 Postgres + pgvector，将代码切片、文档、运行日志摘要等统一存储，降低系统复杂度；
    - 多仓库/大规模代码基线：引入 Qdrant 或 Milvus 作为专用向量库，分 Collection 管理不同项目与数据域。
- 选择原因：
  - Coding OS 主要使用向量检索做：
    - 代码片段召回（按路径/语义）；
    - 历史任务/变更的相似检索；
    - 运行日志与错误模式的向量检索；
  - pgvector 在早期足够简单易用，结合现有关系型数据库即可；规模扩大后，Qdrant/Milvus 在性能与多维过滤上更有优势。
- 相关决策文档：
  - `aia-agent-architecture/decisions/0001-vector-db-selection/`

## 4.3 Agent 编排框架

- 选型结论：
  - 采用「自研 Orchestrator + LangGraph 子流程」的混合方案：
    - 自研 Orchestrator：负责入口统一（CLI/IDE/MCP）、任务派发、权限前置控制与会话管理；
    - LangGraph：在具体 Agent 调度链（例如 AgentTool → runAgent → query）内建模复杂状态机和恢复机制（checkpoint、resumeAgent 等）。
- 选择原因：
  - Claude Code 源码显示，其核心调用链本质上是一套定制的 Agent Orchestrator（AgentTool.call → runAgent → query）；
  - LangGraph 在表达「子 Agent Pipelines + 状态检查点」方面成熟，便于对标 Claude Code 的 fork path / background path 等能力；
  - 自研部分负责与本地工具、权限系统和观测系统深度集成。
- 相关决策文档：
  - `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/`

## 4.4 可观测性与日志方案

- 选型结论：
  - 使用 OpenTelemetry 作为统一追踪标准，结合：
    - 本地/团队级日志系统（文件日志 + 结构化日志收集）；
    - 如有条件，可对接 Prometheus/Grafana 或 SaaS Observability 平台，记录模型调用、工具调用与 Hook 决策。
- 选择原因：
  - Claude Code 将 transcript / metadata / cleanup 纳入完整生命周期，强调「产品化 runtime 的证据」；
  - 本场景需要对 Agent 行为提供可回放的证据，以便审计与调试。
- 相关决策文档：
  - `aia-agent-architecture/decisions/0004-observability-and-logging-selection/`

## 4.5 其他关键技术

- 权限模型与 Hook 政策：
  - 对标 Claude Code 的 Permission / Hook 设计：PreToolUse / PostToolUse / Failure Hooks，配合 allow/ask/deny 以及 preventContinuation；
  - 将高危操作（破坏性、难恢复、共享状态、对外可见、上传到第三方）编码为规则，并与用户设置与团队策略整合。
- Skills / Plugins / MCP 生态：
  - Skill：以 markdown + frontmatter 形式封装工作流，支持 allowed-tools、runtime 变量与 meta messages；
  - Plugin：提供 commands + Skill 目录 + runtime 变量替换，作为扩展工作流与工具集的一等公民；
  - MCP：不仅注入新的 tools，也注入「如何正确使用这些工具」的 instructions，让模型真正感知生态。
- 工程实践：
  - 在实现中应将 Prompt Engineering、Context Engineering、Harness Engineering 结合使用（参考 0005），使 Coding OS 成为「可约束、可治理」的平台。

# 5. 核心组件设计

> 本节重点描述各组件职责与边界，避免「一个 Agent 什么都做」。

- EntryPoints（CLI/IDE/MCP）：
  - 职责：
    - 将用户输入标准化为统一的请求结构（含工作区路径、会话 ID、触发命令等）；
    - 负责 UI 层交互（TUI、IDE 面板、MCP 协议），不参与模型对话细节；
  - 边界：
    - 不直接调用模型与工具，只与 Coordinator 通信。

- Coordinator：
  - 职责：
    - 接收来自不同入口的请求，确定是普通对话、Skill 执行还是 Agent 任务；
    - 管理前台 / 后台 / 远程 Agent 的任务队列和状态；
    - 与 Model Gateway、Tool Runtime、Observability 系统集成；
  - 边界：
    - 不落地业务逻辑细节，而是调度 AgentTool/SkillTool 等更细粒度组件。

- Prompt Assembly 引擎：
  - 职责：
    - 维护 system prompt 的模块化 Section（身份与定位、系统规范、做事哲学、风险操作规则、工具使用规则、Session-specific guidance、output efficiency、tone & style 等）；
    - 实现静态前缀 + 动态后缀的拼装逻辑，管理 SYSTEM_PROMPT_DYNAMIC_BOUNDARY 与缓存策略；
  - 边界：
    - 不直接关心具体业务任务，仅根据上下文与 Feature 开关拼装 Prompt。

- AgentTool / SkillTool：
  - AgentTool 职责：
    - 解析调用参数（description、prompt、subagent_type、model、run_in_background、isolation 等）；
    - 决定是 fork path（继承主上下文 + cache）还是 normal path（独立 Agent）；
    - 构建 Agent-specific ToolUseContext、注册 MCP servers、frontmatter hooks 与 skills；
    - 调用 runAgent / query 执行子 Agent；
  - SkillTool 职责：
    - 加载 Skill 包，解析 frontmatter，注入 allowed-tools 与 runtime 变量；
    - 将 Skill 相关 meta 内容注入到初始消息流中；
  - 边界：
    - 不负责最终模型调用与工具执行，只负责拼装上下文与能力集合。

- Built-in Agents（Explore / Plan / Verification / General Purpose 等）：
  - 各自的职责与边界应类似 Claude Code 定义：
    - Explore：严格只读；
    - Plan：只读 + 输出实现方案，不做修改；
    - Verification：专注对抗验证与命令执行，不再改代码；
    - General Purpose：在权限模型约束下做实现与局部修改；
  - 边界：
    - 不相互混用职责，避免「一个 Agent 又探索又规划又实现又验收」。

- Tool Runtime 层（toolExecution + toolHooks）：
  - 职责：
    - 统一处理工具调用：输入校验（schema parse + validateInput）、PreToolUse Hooks、Permission Flow、实际工具执行、PostToolUse Hooks、错误处理与 telemetry；
  - 边界：
    - 不关心具体 Agent，只对工具调用请求做治理与审计。

- Model Gateway：
  - 职责：
    - 统一处理模型调用（包括 Prompt 源、Tool use、MCP 指令等），并记录调用日志、成本与错误；
  - 边界：
    - 不实现编排逻辑，只做「推理服务接口」。

- Observability & Telemetry：
  - 职责：
    - 记录每次 Agent 运行的 transcript、Tool use、Hook 决策、Permission 结果与最终 verdict；
    - 提供回放与查询接口，支持按仓库、分支、时间、开发者等维度过滤；
  - 边界：
    - 不参与实时决策，仅提供 evidence 与可视化能力。

# 6. 关键流程说明

## 6.1 复杂需求从探索到验证的全链路

1. 开发者通过 IDE 插件触发「实现需求 X」命令；
2. Coordinator 根据当前会话状态，先调用 Explore Agent：
   - Explore Agent 只使用 Glob/Grep/FileRead/Bash(只读命令) 等工具，构建代码地图与关键文件列表；
3. Coordinator 将 Explore 结果与需求描述一并传给 Plan Agent：
   - Plan Agent 输出 step-by-step 实现方案、Critical Files for Implementation 与风险清单；
4. 经开发者确认后，Coordinator 触发 General Purpose Agent：
   - 在 Permission + Hook 约束下执行 FileEdit/FileWrite 等工具，完成实际代码修改；
5. 修改完成后，Coordinator 触发 Verification Agent：
   - 运行 lint/test/build 等命令，做 adversarial probes，并输出 PASS/FAIL/PARTIAL verdict；
6. 整个过程的 transcript / telemetry 被记录，供后续审计与学习。

## 6.2 高危操作的权限与 Hook 流程

1. Agent 决定调用某个高危工具（如 Bash 写入命令、删除文件、批量重构）；
2. Tool Runtime 首先做输入校验（schema parse + validateInput）；
3. 运行 PreToolUse Hooks：
   - 可能修改输入（updatedInput）、给出建议（message）、或决定 permissionBehavior（allow/ask/deny）；
4. Permission 模型综合设置与 Hook 结果，决定是否需要用户确认或直接拒绝；
5. 工具执行后，运行 PostToolUse / Failure Hooks，可能追加上下文、阻断后续流程或更新 MCP 输出；
6. 全链路记录在 telemetry 中。

## 6.3 Skill / Plugin 驱动的团队工作流

1. 团队将常见工作流封装为 Skill（markdown + frontmatter + allowed-tools）；
2. 插件系统加载 Skill 目录，生成对应命令与元数据；
3. 开发者通过命令或菜单触发 Skill；
4. SkillTool 构建执行上下文，并调用合适的 Agent 组合完成任务；
5. 执行过程受 Permission/Hooks 约束，结果可复用与回放。

# 7. 数据与存储设计

- 数据类型与来源：
  - 源码与配置：来自本地或远程 Git 仓库；
  - 任务与会话元数据：记录于本地数据库（SQLite/Postgres）或团队级服务；
  - 向量索引：代码片段、文档、日志摘要等存储在 pgvector/Qdrant/Milvus 中；
  - Telemetry 数据：存储在结构化日志或时序数据库中，用于分析与回放。

- 存储与索引：
  - 代码与配置仍以 Git 为真相来源；
  - 会话与任务以表结构管理，支持按仓库、分支、开发者等维度查询；
  - 向量库按「仓库/目录/文件类型」分 Collection 管理，便于分区扩展。

- 数据生命周期：
  - Telemetry 与会话数据设置保留周期，可按项目/团队策略清理；
  - 向量索引根据仓库更新策略进行增量更新与重建。

# 8. 非功能性要求

- 性能与延迟：
  - 常见操作（小范围修改、局部重构）应在数秒内给出结果；
  - 大型代码库的探索与规划可以接受更长时间，但应有进度反馈。

- 可用性与容错：
  - 工具调用失败时，应优先通过 Hook 与 Verification 机制给出可理解的错误说明，而不是 silent fail；
  - 在模型或网络异常时，应降级为只读探索模式，不允许执行高危写操作。

- 安全与合规：
  - 所有破坏性操作必须可追溯（谁触发、何时、对哪些文件/命令）；
  - 对企业环境，应可配置「禁止将敏感代码通过云端 LLM 处理」的策略，必要时只允许本地/自托管模型。

- 成本与资源：
  - 通过 prompt cache boundary、fork cache 复用等策略降低 token 成本；
  - 对于后台任务与批量分析，优先安排在低峰期或使用低成本模型。

# 9. 风险、边界与取舍

- 已知风险：
  - 过度复杂的架构可能提高上手成本，小团队难以一次吃透；
  - 如果权限与 Hook 策略配置不当，可能要么过于严格（降低效率），要么过于宽松（安全风险）。

- 边界与不做的事：
  - 不要求一次性覆盖所有 IDE 与语言，优先支持 1–2 种主语言与主流编辑器；
  - 不追求「完全自动化交付」，始终保留人工审核与确认环节。

- 设计取舍：
  - 在系统性治理（Permission/Hook/Telemetry）与实现复杂度之间取平衡：
    - MVP 可先实现简化版的 Hook 与权限模型，再逐步增强；
    - 对最关键的工具（Bash、文件写入）优先实现完整的治理链路。

# 10. 实施计划与里程碑（可选）

- v0.1（MVP 本地单机版）：
  - 支持 CLI/TUI 入口、Explore/Plan/General Purpose Agent 基本链路；
  - 实现基础 Prompt Assembly 与少量工具（FileRead/FileEdit/Grep/Glob）；
  - 仅本地日志记录，不接入复杂观测平台。

- v0.2（多 Agent 与权限治理）：
  - 引入 Verification Agent 与对抗验证流程；
  - 实现 Tool Runtime + Pre/Post Hooks + 简化 Permission 模型；
  - 增加 Telemetry 结构化记录与简单可视化。

- v0.3（技能与插件生态）：
  - 引入 Skill / Plugin / MCP 支持，让模型「感知」扩展能力；
  - 与团队级配置中心与策略系统集成；
  - 支持后台 Agent、远程工作区与多入口（IDE/MCP）。

# 11. 关联文档

- ADR 决策文档：
  - `aia-agent-architecture/decisions/0001-vector-db-selection/`
  - `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/`
  - `aia-agent-architecture/decisions/0003-model-and-inference-service-selection/`
  - `aia-agent-architecture/decisions/0004-observability-and-logging-selection/`
  - `aia-agent-architecture/decisions/0005-engineering-design/`
  - `aia-agent-architecture/decisions/0006-foundation-models-selection/`
- 研究与调研笔记：
  - `research/claude-code-architecture-study.md`
