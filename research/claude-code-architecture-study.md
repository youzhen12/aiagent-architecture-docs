---
title: Claude Code 源码架构研究说明
author: [Xiao Tan]
source: docs/claude code.md
last_updated: 2026-04-01
---

## 1. 研究背景与目标

- 研究对象：
  - 基于 npm 包 `@anthropic-ai/claude-code` 中 `cli.js.map` 还原出的完整源码，对其进行系统性架构分析。
- 研究范围：
  - 整体源码结构与模块划分；
  - System Prompt 架构与 `prompts.ts` 的真实地位；
  - Agent Prompt 与内置 Agents（Explore/Plan/Verification 等）的职责分工；
  - Agent 调度链（AgentTool → runAgent → query）与子 Agent runtime；
  - Skills / Plugins / Hooks / MCP 生态；
  - 权限模型、工具执行链与 Hook 政策；
  - Prompt cache、上下文经济学与产品化 runtime 细节。
- 研究目标：
  - 说明 Claude Code 的「护城河」不在于某个神秘 prompt，而在于一整套 Agent Operating System 式的工程化架构；
  - 为本仓库中的 `scene-claude-code-assistant` 场景提供架构输入与设计原则。

## 2. 整体架构认知：从 CLI 到 Agent Operating System

- 源码顶层结构显示，Claude Code 并非一个简单的 CLI 包装器，而是一个小型的「Agent 平台」：
  - `src/entrypoints/`：多种入口（CLI、init 流程、MCP、SDK），体现平台化思路；
  - `src/constants/`：集中管理 system prompt、风险提示、输出规范等常量；
  - `src/tools/`：定义与实现所有工具，包括 AgentTool、SkillTool 及各种文件/搜索/任务工具；
  - `src/services/`：运行时服务层，负责工具执行、MCP、分析与遥测等；
  - `src/commands/`：命令系统，是生态的主要交互入口（/mcp、/skills、/tasks 等）；
  - `src/coordinator/`：多 Agent 协调器；
  - `src/memdir/`：记忆与 memory prompt；
  - `src/plugins/` 与 `src/utils/plugins/`：插件生态；
  - `src/hooks/` 与 `src/utils/hooks.js`：_hook_ 机制；
  - `src/tasks/`：本地/远程/异步 Agent 任务。
- 关键结论：
  - Claude Code 的真实形态更接近「Agent Operating System」而不是「一个 coding prompt」；
  - 其核心价值在于：Prompt 架构 + Tool Runtime 治理 + Permission/Hook 模型 + Agent 专业化 + 可观测性 一体化设计。

## 3. System Prompt 架构：模块化装配与上下文经济学

- `src/constants/prompts.ts` 是最关键的文件之一，其核心思想包括：
  - Prompt 是模块化 runtime assembly，而不是一段静态文本；
  - 通过 `getSystemPrompt()` 将多个 section 以「静态前缀 + 动态后缀」拼装起来：
    - 静态前缀部分适合做 prompt cache（身份与定位、系统规则、做事哲学、风险动作规则、工具使用规则、输出效率、语气风格等）；
    - 动态后缀部分根据当前会话与环境注入（session guidance、memory、模型 override、语言/输出风格、MCP instructions、scratchpad、function result clearing、summarize tool results、token budget 等）。
- `SYSTEM_PROMPT_DYNAMIC_BOUNDARY` 明确划定了缓存边界：
  - 边界前尽量可缓存，边界后是用户/会话特定内容，不能随意变更否则会破坏 cache 逻辑；
  - 这体现了「prompt cache economics」的设计：连 system prompt 的 token 成本与命中率都被工程化考虑。
- 核心价值：
  - 把 Prompt 从「一段字符串」提升为「可组合、可优化、可缓存的运行时资源」。

## 4. 行为规范与工程哲学：把「好习惯」写进系统

- Claude Code 在 Prompt 中大量编码了「工程师行为规范」，典型例子：
  - `getSimpleDoingTasksSection()`：
    - 不要加用户没要求的功能；
    - 不要过度抽象，不要胡乱重构；
    - 不要随意加 comments/docstrings/type annotations；
    - 不要做不必要的 error handling/fallback/validation；
    - 先读代码再改代码，不要轻易新建文件；
    - 失败时先诊断再换策略；
    - 注意安全漏洞，清理无用代码，不搞「兼容性垃圾」；
    - 如实汇报结果，不能假装测试通过。
  - `getActionsSection()`：
    - 明确定义「需要确认」的风险动作：破坏性操作、难以恢复操作、修改共享状态、对外可见操作、上传第三方；
    - 强调不要用破坏性操作当捷径，遇到陌生状态先调查；
  - `getUsingYourToolsSection()`：
    - 明确工具使用语法与优先级（读文件优先 FileRead，改文件优先 FileEdit，搜索优先 Glob/Grep，Bash 只保留给必要场景等）；
    - 鼓励并行使用工具，禁止滥用 shell；
  - 输出效率与语气风格 section：
    - 强调先说结论、少铺垫、不滥用 emoji、使用统一的引用格式等。
- 核心结论：
  - Claude Code 的稳定性不是来自「更聪明的模型」，而是来自把「好行为」制度化到 Prompt 与 runtime 规则中。

## 5. Agent 专业化与协作模式

- 从源码可以确认多种内置 Agent：
  - General Purpose Agent；
  - Explore Agent（严格只读的代码探索专家）；
  - Plan Agent（纯规划，不做修改）；
  - Verification Agent（对抗式验证者）；
  - Claude Code Guide Agent、Statusline Setup Agent 等。
- Agent Prompt 对各 Agent 职责划分非常明确：
  - Explore：
    - 绝对只读，不允许创建/修改/删除/移动文件，不运行改变系统状态的命令；
    - 核心能力是快速用 Glob/Grep/FileRead/Bash(只读) 浏览代码，并尽量并行使用工具；
  - Plan：
    - 只读，输出详细的实现计划与关键文件列表；
  - Verification：
    - 任务是「try to break it」，必须运行 build/test/lint 等命令，对不同变更类型做针对性验证，并输出带命令与输出的 verdict；
  - General Purpose：
    - 在权限与 Hook 约束下实际修改代码，但不负责探索与对抗验证。
- 关键认知：
  - 多 Agent 不是为了「看起来高级」，而是为了降低角色混杂导致的行为漂移，将复杂任务拆成探索-规划-实现-验证的明确分工。

## 6. Agent 调度链与子 Agent Runtime

- Agent 调用链大致为：
  - 主模型决定调用 Agent 工具 → `AgentTool.call()` 解析参数与上下文 → 选择 built-in / custom / fork / background / remote 等模式 → 构建 system prompt 与 messages → 构建工具池与 ToolUseContext → 调用 `runAgent()` → `runAgent()` 内部再调用 `query()` 进入模型对话主循环。
- `AgentTool.call()` 的职责远超「转发到子 Agent」：
  - 解析 description/prompt/subagent_type/model/run_in_background/isolation 等参数；
  - 判断 multi-agent teammate 是否需要 spawn；
  - 处理 team context、background 权限与 fork path；
  - 根据权限规则过滤 Agent；
  - 构建 Agent-specific 上下文与工具集。
- `runAgent()` 负责构造完整的子 Agent runtime：
  - 初始化 agent-specific MCP servers；
  - 过滤/克隆 context messages；
  - 处理 file state cache 与 read-only agent 的特殊优化；
  - 构建 permission mode、工具集与 ToolUseContext；
  - 注册 frontmatter hooks 与 skills，合并 MCP tools；
  - 执行 query() 并记录 transcript；
  - 在结束时清理 MCP、hooks、perfetto、todos、bash tasks 等资源。
- 关键价值：
  - 将复杂的子 Agent 生命周期、上下文构造与资源清理，封装到明显的 runtime 层中，而非散落在业务逻辑代码里。

## 7. Skills / Plugins / Hooks / MCP：让生态「可被模型感知」

- Skill：
  - 本质是 prompt-native workflow package：markdown + frontmatter metadata + allowed-tools + runtime 变量；
  - 通过命令/标签等方式注入当前会话，减少重复工作流描述。
- Plugin：
  - 提供 commands、Skill 目录、commandsMetadata、userConfig、shell frontmatter、allowed-tools、model/effort hints、user-invocable 标记等；
  - 支持 runtime 变量替换（如 `${CLAUDE_PLUGIN_ROOT}`、`${CLAUDE_SESSION_ID}` 等），将系统状态与插件能力串起来。
- Hook：
  - 作为 runtime policy layer，支持 PreToolUse、PostToolUse、PostToolUseFailure；
  - Hook 可以输出 message、blockingError、updatedInput、permissionBehavior、preventContinuation、additionalContext、updatedMCPToolOutput 等，真正参与控制流与权限决策；
  - 权限含义通过 `resolveHookPermissionDecision()` 严格嵌入总权限模型，避免侧路绕过。
- MCP：
  - 不仅注入工具，还通过 `getMcpInstructionsSection()` 等机制，把「如何使用这些工具」的说明一起拼入 system prompt，使模型真正理解扩展能力。
- 关键洞见：
  - 好的生态不是「可安装」，而是「模型可感知」；
  - Skills/Plugins/MCP 只有在 Prompt 与 runtime 里被显式建模，才能充分发挥作用。

## 8. 工具执行链与权限治理

- `toolExecution.ts` 定义了工具执行主线：
  1. 查找 tool；
  2. 解析 MCP metadata；
  3. 做输入 schema 校验与 validateInput；
  4. 对 Bash 等高危工具做 speculative classifier check；
  5. 运行 PreToolUse hooks；
  6. 解析 hook 权限结果并走统一 permission 决策；
  7. 再次根据 updatedInput 修正输入；
  8. 真正执行 tool.call()；
  9. 记录 analytics/tracing/OTel；
  10. 运行 PostToolUse hooks；
  11. 处理结构化输出或 tool_result；
  12. 失败时运行 PostToolUseFailure hooks。
- 输入校验与错误处理：
  - 使用 Zod 等 schema parse + tool-specific validateInput 拦截低级错误；
  - 失败时返回结构化的 tool_result 错误信息并记录 `tengu_tool_use_error`，避免错误参数直接污染执行层。
- 权限决策：
  - Hook 可以给出 allow/ask/deny，但其 allow 也不能绕过 settings 中的更严格规则；
  - ask 类型 hook 会作为 forceDecision 继续传递；
  - deny 类型立即生效。
- 关键特点：
  - 工具调用不是「模型字符串 → 直接执行」，而是「一条可治理、可观测、可扩展的 runtime pipeline」。

## 9. Claude Code 的真正护城河

- 综上，Claude Code 的优势主要体现在：
  - Prompt Architecture：模块化、可缓存、可注入 instructions 的 system prompt 架构；
  - Tool Runtime Governance：统一的工具执行 pipeline 与 Hook/Permission 模型；
  - Agent Specialization：探索、规划、实现、验证等角色明确分工；
  - Skill & Plugin 生态：以 prompt-native 形式封装工作流，支持 runtime 变量与扩展；
  - MCP Integration：同时注入工具与行为说明，使扩展真正被模型感知；
  - Context Economics：通过边界、fork cache、压缩与 resume 等机制，将上下文视为稀缺资源管理；
  - Productized Runtime：对 transcript、telemetry、cleanup 与任务系统的系统化设计。

## 10. 对场景架构的启发

- 对 `scene-claude-code-assistant` 的直接启发包括：
  - 不要只抄 Claude Code 的 system prompt，而是要复制其「Prompt + Tool + Permission + Hook + Agent + Skill + MCP + Telemetry」的一体化思路；
  - 在本地 Coding OS 的设计中，应显式地：
    - 定义 Prompt Assembly 引擎与缓存边界；
    - 按探索/规划/实现/验证拆分 Agent，并用 AgentTool 建立子 Agent runtime；
    - 用 Tool Runtime/Hook/Permission 统一治理所有工具执行；
    - 用 Skill/Plugin/MCP 构建「模型可感知」的生态；
    - 将 transcript/telemetry 作为一等公民，支持回放和评估。

- 建议的工程实践：
  - 在本仓库中配合 `0005-engineering-design`：
    - 在 Prompt Engineering 文档中纳入 Claude Code 的 Prompt 分层与行为规范模式；
    - 在 Context Engineering 文档中扩展「prompt cache boundary」「fork cache」等上下文经济学实践；
    - 在 Harness Engineering 文档中对齐工具执行链、权限模型与 Hook 政策的最佳实践。
