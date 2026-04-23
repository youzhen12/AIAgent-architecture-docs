---
title: "Flocks - AI 原生 SecOps 平台总体架构设计"
status: draft
version: 0.1.0
owner: AIAgent 团队
last_updated: 2026-04-21
scene_id: scene-flocks-secops
---

# 1. 背景与目标

- **场景简介**：
  - Flocks 是一个 AI 原生 SecOps 平台，面向安全运维、开发、运维角色，提供多智能体协作的 AI 辅助工作台。  
  - 通过 CLI、TUI、WebUI、IM Bot 等多端接入，支持代码审计、漏洞分析、应急响应、日志分析等典型 SecOps 任务。  
- **业务问题**：
  - 安全运维任务复杂度高，单一大模型难以独立完成，需要多智能体协同与丰富的工具链支持。  
  - 传统安全工具分散，缺乏统一的 AI 辅助入口，知识难以沉淀与复用。  
- **AIAgent 目标**：
  - 提供「智能编排 + 多 Agent 协作 + 丰富工具 + 工作流自动化」的一站式 SecOps 能力。  
  - 降低安全工程师、开发人员、运维人员的操作门槛，提升效率。  
  - 通过 Skill 和记忆系统沉淀高价值经验，实现持续学习与优化。  
- **文档目标**：
  - 描述 Flocks 的整体架构、关键技术选型与组件职责，为后续实现和对标提供参考。  
  - 将 Flocks 的设计映射到阿里云产品体系，产出 MaaS 落地方案。  
- **非目标**：不详细扩展具体工具的内部实现细节（如 LSP 客户端、MCP 协议底层等）。  

# 2. 业务与使用场景

- **主要用户与角色**：
  - 安全工程师（SecOps）：负责代码审计、漏洞分析、应急响应、威胁检测。  
  - 开发人员（Dev）：负责代码审查、重构辅助、文档生成、安全编码指导。  
  - 运维人员（Ops）：负责日志分析、配置管理、故障排查、监控告警处理。  
  - 团队负责人：关注整体安全状况、任务进度、知识沉淀与团队协作效率。  

- **关键使用场景**：
  1. **代码安全审计**：安全工程师通过 CLI/TUI 启动审计任务，主 Agent 将任务拆分为依赖分析、漏洞扫描、敏感信息检测等子任务，由专职子 Agent 并行执行，最终生成审计报告。  
  2. **漏洞应急响应**：发现 CVE 后，通过 WebUI 触发应急流程，Agent 自动关联影响范围、生成修复方案、执行代码修改、验证修复结果。  
  3. **日志异常分析**：运维人员通过自然语言查询日志，Agent 自动识别异常模式，关联监控指标，生成排查建议与修复脚本。  
  4. **工作流自动化**：定义标准化安全审计工作流，定时执行，自动输出报告并推送至企业微信/飞书。  

- **典型业务流程**：
  - 流程 A：代码安全审计（探索 → 规划 → 执行 → 验证 → 报告）。  
  - 流程 B：漏洞应急响应（发现 → 评估 → 修复 → 验证 → 沉淀）。  

# 3. 整体架构概览

- **一句话描述整体架构风格**：
  - 「多端接入 + FastAPI 服务层 + 多智能体编排 + 丰富工具链 + 工作流引擎 + 统一存储」。

- **架构图占位**：
  - [ ] **逻辑架构图**：
    ```
    CLI / TUI / WebUI / IM Bot
            ↓
    ┌─────────────────────────────┐
    │     FastAPI Server (8000)    │
    │   Middleware + 36+ Routers   │
    └──────────────┬──────────────┘
                   ↓
    ┌────────────────────────────────────────────────┐
    │              业务逻辑层                         │
    │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
    │  │ Session │ │ Agent   │ │  Tool   │          │
    │  │ Manager │ │ Registry│ │ Registry│          │
    │  └─────────┘ └─────────┘ └─────────┘          │
    │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
    │  │Workflow │ │  Task   │ │ Memory  │          │
    │  │ Engine  │ │ Center  │ │ System  │          │
    │  └─────────┘ └─────────┘ └─────────┘          │
    └────────────────────────┬───────────────────────┘
                             ↓
    ┌────────────────────────────────────────────────┐
    │              基础设施层                         │
    │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
    │  │ Storage │ │  MCP    │ │  LSP    │          │
    │  │(SQLite) │ │ Manager │ │ Client  │          │
    │  └─────────┘ └─────────┘ └─────────┘          │
    └────────────────────────────────────────────────┘
    ```
  - [ ] **部署架构图**：单机部署（Docker 或本地安装）→ 服务端（FastAPI + SQLite）→ 多客户端接入。

- **核心组件清单**：
  - **用户入口**：CLI（Typer/Click）、TUI（bun）、WebUI（Vite）、IM Bot（企业微信 WebSocket、飞书 Open API）。  
  - **Orchestrator / Agent 编排层**：主 Agent（如 Rex）负责任务拆解与子 Agent 调度，通过 `delegate_task` 工具委托子任务。  
  - **Agent 集合**：主 Agent（primary）+ 子 Agent（subagent，如 build、plan、general），支持自定义 Agent 插件。  
  - **工具层**：核心工具（read/write/edit/bash/grep/glob/list）、代码工具（codesearch/lsp_task）、系统工具（memory/batch/skill）、任务工具（todo/plan/run_workflow）、Agent 工具（delegate_task）。  
  - **模型与向量库**：统一模型网关（LiteLLM）对接多提供商（OpenAI/Anthropic/Google/自定义），SQLite 存储会话与记忆。  
  - **工作流引擎**：支持定义、编译、执行自动化工作流，多种运行时（async/REPL/service）。  
  - **监控与日志**：内置日志系统 + Langfuse 可观测性 + SSE 事件广播。  

# 4. 核心技术选型与原因

## 4.1 模型与推理服务

- **选型结论**：
  - 使用 LiteLLM 作为统一模型网关，对接 OpenAI、Anthropic、Google 等多家 LLM 提供商，支持自定义 Provider。  
- **选择原因**：
  - LiteLLM 提供 OpenAI 兼容接口，简化多模型集成，支持动态切换模型与提供商。  
  - 通过 `provider/` 模块管理凭证、模型目录、默认模型，支持从环境变量迁移到安全存储。  
- **参考决策文档**：
  - `aia-agent-architecture/decisions/0003-model-and-inference-service-selection.md`
  - `aia-agent-architecture/decisions/0006-foundation-models-selection.md`

## 4.2 知识存储与检索方案

- **选型结论**：
  - 使用 SQLite（aiosqlite + sqlalchemy）作为统一存储后端，键值模式（`namespace:key`）。  
  - 记忆系统（`flocks/session/features/memory.py`）支持事实/偏好/模式存储，跨会话复用。  
- **选择原因**：
  - SQLite 轻量、嵌入部署，适合单机 SecOps 场景。  
  - 键值模式灵活，便于扩展新的数据类型（会话、消息、Skill 缓存等）。  
- **参考决策文档**：
  - `aia-agent-architecture/decisions/0008-knowledge-store-and-retrieval-selection.md`
  - `aia-agent-architecture/decisions/0001-vector-db-selection.md`

## 4.3 Agent 编排框架

- **选型结论**：
  - 自研 Agent 编排框架：通过 `session/runner.py` 实现会话主循环，`agent/registry.py` 管理 Agent 注册与发现，`tool/` 系统支持 `delegate_task` 子 Agent 委托。  
- **选择原因**：
  - Flocks 定位为完整平台，需要深度集成会话、工具、工作流、记忆等系统，自研编排更灵活。  
  - 通过 `AgentInfo.mode` 区分 primary/subagent，支持动态 Prompt 注入（`prompt_builder` 钩子）。  
- **参考决策文档**：
  - `aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection.md`

## 4.4 可观测性与日志方案

- **选型结论**：
  - 内置日志系统（`flocks/utils/log.py`）+ Langfuse 可观测性 + SSE 事件广播（`server/routes/event.py`）。  
- **选择原因**：
  - 内置日志轻量且集成度高，适合快速调试与生产监控。  
  - Langfuse 提供 LLM 调用追踪、Token 统计、延迟分析，适合 AI 应用可观测性。  
- **参考决策文档**：
  - `aia-agent-architecture/decisions/0004-observability-and-logging-selection.md`

## 4.5 工程设计重点

- **提示词工程**：
  - 通过 `session/prompt/` 目录管理多模型 Prompt 模板（Anthropic、OpenAI、Gemini 等），支持动态注入。  
- **上下文工程**：
  - 会话级上下文管理（`session/core/session_state.py`、`session/core/turn_state.py`），支持压缩（compaction）保留上下文。  
- **驯控工程 / Harness**：
  - `session/session_loop.py` 实现主循环，控制 LLM 调用 → Tool 执行 → 结果返回的循环。  
  - 权限系统（`permission/`、`Ruleset`）控制工具访问，支持会话级权限配置。  
- **参考决策文档**：
  - `aia-agent-architecture/decisions/0005-engineering-design.md`

# 5. 核心组件与流程

## 5.1 组件清单与职责

| 组件 | 职责 | 关键文件 |
|------|------|----------|
| **入口层** | CLI/TUI/WebUI/IM Bot 接入，请求路由至 API | `flocks/cli/main.py`, `tui/`, `webui/`, `flocks/channel/` |
| **FastAPI 服务** | HTTP API、SSE 流式响应、Middleware、Lifespan | `flocks/server/app.py`, `flocks/server/routes/` (36 模块) |
| **Session 管理** | 会话创建、生命周期、消息、压缩、分支、归档 | `flocks/session/session.py`, `flocks/session/message.py`, `flocks/session/core/` |
| **Agent Registry** | Agent 定义、注册、发现、动态 Prompt 注入 | `flocks/agent/agent.py`, `flocks/agent/registry.py`, `flocks/agent/agents/` |
| **Tool Registry** | 工具注册、执行、权限检查、热重载插件 | `flocks/tool/registry.py`, `flocks/tool/code/`, `flocks/tool/system/` |
| **Workflow Engine** | 工作流定义、编译、执行、LLM 节点 | `flocks/workflow/engine.py`, `flocks/workflow/runner.py` |
| **Task Center** | 任务调度、队列执行、内置任务种子 | `flocks/task/manager.py`, `flocks/task/plugin.py` |
| **Memory System** | 跨会话记忆、事实/偏好/模式存储 | `flocks/session/features/memory.py` |
| **Skill System** | Skill 定义、缓存、文件监听、热重载 | `flocks/skill/skill.py`, `flocks/tool/skill/` |
| **MCP Manager** | MCP 服务器连接、配置、生命周期 | `flocks/mcp/` |
| **LSP Client** | 语言服务器协议集成、代码分析 | `flocks/lsp/` |
| **Provider Manager** | LLM 提供商管理、模型目录、凭证安全 | `flocks/provider/` |
| **Storage** | 统一存储抽象（SQLite 键值模式） | `flocks/storage/storage.py` |

## 5.2 关键流程说明

### 流程 A：代码安全审计（全链路）

1. **启动审计**：用户通过 CLI 输入 `flocks session "审计代码库安全问题"`，创建 Session。  
2. **主 Agent 接手**：Rex（主 Agent）分析意图，拆分为子任务：依赖分析、漏洞扫描、敏感信息检测。  
3. **子 Agent 执行**：通过 `delegate_task` 委托给专职子 Agent（如 `secops-audit-agent`），子 Agent 调用 `codesearch`、`grep`、`websearch` 等工具。  
4. **结果汇聚**：子 Agent 完成后返回结果，主 Agent 汇总并生成审计报告。  
5. **报告输出**：报告写入工作区（`~/.flocks/workspace/outputs/<date>/`），通过 SSE 广播至 WebUI/TUI。  

### 流程 B：工具调用与权限控制

1. **工具请求**：Agent 在会话中请求调用工具（如 `bash` 执行命令）。  
2. **权限检查**：`permission/` 模块检查会话级权限规则（`Ruleset`），决定是否允许执行。  
3. **执行工具**：`ToolRegistry.execute()` 调用具体工具实现，返回结果。  
4. **热重载**：`ToolRegistry.start_watcher()` 监听 `~/.flocks/plugins/tools/`，工具变更后自动重载。  

### 流程 C：工作流自动化

1. **定义工作流**：用户在 `.flocks/plugins/workflows/` 目录定义工作流（JSON/YAML/Markdown）。  
2. **同步与编译**：`server/routes/workflow.py` 同步工作流至 Storage，`workflow/compiler.py` 编译。  
3. **定时执行**：Task Center 根据 cron 表达式触发工作流执行。  
4. **LLM 节点**：工作流中的 LLM 节点调用模型，执行分析或生成任务。  
5. **输出报告**：工作流完成后，生成报告并推送至 IM 渠道（企业微信/飞书）。  

# 6. 数据与存储设计

- **数据类型与来源**：
  - 会话数据：用户输入、Agent 响应、工具调用记录、消息历史。  
  - 配置数据：`flocks.json`（全局配置）、`agent.yaml`（Agent 定义）、`.secret.json`（安全凭证）。  
  - 知识数据：Skill 文档、记忆事实/偏好/模式、工作流定义。  
  - 输出数据：分析报告、审计报告、工作流输出（`~/.flocks/workspace/outputs/<date>/`）。  

- **存储与索引方案**：
  - **SQLite**：统一存储后端，键值模式（`namespace:key`）。  
    - `session:*`：会话元数据。  
    - `message:*`：消息数据。  
    - `workflow:*`：工作流定义与状态。  
    - `task:*`：任务数据。  
    - `memory:*`：记忆数据。  
  - **文件系统**：
    - `~/.flocks/`：用户级配置、插件、工作区。  
    - `session/prompt/`：Prompt 模板。  
    - `skills/`：Skill 文件。  

- **数据生命周期**：
  - 会话按状态（active/archived/deleted）管理，支持软删除。  
  - 压缩（Compaction）机制自动压缩长对话历史，保留上下文。  
  - 记忆数据定期更新，Skill 缓存失效时自动重建。  

# 7. 非功能性要求

| 维度 | 要求 |
|------|------|
| **性能与延迟** | 一般交互 P95 延迟 ≤ 3s；复杂工具调用链可适当放宽 |
| **可用性与容错** | 模型/工具异常时降级为模板化回答；SSE 事件广播保障前端实时性 |
| **安全与合规** | 凭证安全存储（`.secret.json`）；工具权限控制；禁止硬编码凭据 |
| **可观测性** | 内置日志 + Langfuse 追踪 + SSE 事件流 |
| **扩展性** | 插件/Skill/Hook 系统支持热重载；MCP 协议接入外部工具 |
| **成本** | 支持多模型按需切换；Token 统计与用量追踪 |

# 8. 风险、边界与取舍

- **已知风险**：
  - 长会话上下文可能导致模型响应质量下降，压缩策略需持续优化。  
  - 多子 Agent 协作时任务拆分不当可能导致效率低下。  
  - 工具执行的安全边界需严格控制，防止误操作。  
- **边界与不做的事**：
  - 不提供完整的企业级多租户支持（定位单机/小团队场景）。  
  - 不替代专业安全工具（如 SAST/DAST 扫描器），而是辅助调用与分析。  
- **取舍**：
  - 在「自动化程度」与「安全控制」之间倾向可控：高危工具调用需显式确认。  
  - 选择 SQLite 而非复杂数据库：简化部署，适合单机场景，但扩展性受限。  

# 9. 实施计划（可选）

| 阶段 | 目标 | 里程碑 |
|------|------|--------|
| **阶段 1** | 理解 Flocks 架构，梳理核心组件与流程 | 产出架构总览文档 |
| **阶段 2** | 映射到阿里云产品体系，设计 MaaS 落地方案 | 产出 MaaS 解决方案设计文档 |
| **阶段 3** | 验证方案可行性，POC 核心能力 | 完成 POC，输出验证报告 |
| **阶段 4** | 生产化落地，适配企业级需求 | 上线试运行 |

# 10. 关联文档

- **项目分析文档**：`../flocks-project-analysis.md`
- **研究与调研笔记**：（预留）
- **工程设计**：
  - 提示词 / 上下文 / 驭控工程：`../aia-agent-architecture/decisions/0005-engineering-design/`
  - Agent 编排框架选型：`../aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection.md`
  - 可观测性与日志方案：`../aia-agent-architecture/decisions/0004-observability-and-logging-selection.md`
  - 工具沙盒与执行控制：`../aia-agent-architecture/decisions/0010-tool-sandbox-and-execution-control/`
