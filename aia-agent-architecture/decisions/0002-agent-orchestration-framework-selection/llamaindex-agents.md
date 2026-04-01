---
title: "使用 LlamaIndex Agents / Workflow 进行编排"
status: draft
---

# 描述

LlamaIndex 最初定位为「数据/文档中心的 RAG 框架」，随后引入了 Agents 与 Workflow 等模块，用于构建基于工具/检索/多步骤推理的智能体应用。其核心优势在于：

- 数据连接与索引能力强（大量 Connector、索引类型、GraphRAG 等）；
- 在此基础上提供了 ReAct Agent、OpenAIAgent、自定义 Workflow（DAG/State Machine 风格）等组件，让「面向数据的 Agent 流程」相对容易落地。

与 LangGraph 相比，LlamaIndex Agents 更偏向「RAG-first + Agent 能力」：即在 RAG 场景下顺带提供 Agent/Workflow，而不是从一开始就以「多 Agent 编排引擎」为核心目标。

# 优点

- 数据与检索一体化：
  - LlamaIndex 本身就是以「索引 + 检索 + 路由」为核心，Agent/Workflow 与这些能力深度集成；
  - 适合以复杂 RAG（多数据源、多索引、多路由决策）为中心的 AIAgent 系统。
- 提供多种 Agent 模式：
  - 基于 ReAct 模式的通用 Agent；
  - 针对 OpenAI/兼容接口的 OpenAIAgent；
  - 可通过自定义工具、检索器、Routing 逻辑扩展。
- Workflow API 支持 DAG/状态机式流程：
  - 可以将复杂流程拆成多节点，节点间通过显式的数据/状态传递；
  - 适合表达「先检索 A，再检索 B，再综合回答」等多步数据处理管线。
- 与向量库与存储生态集成良好：
  - 原生支持多种向量数据库、存储与查询后端，减少在「RAG + Agent」场景下的胶水代码。

# 缺点

- Python 生态为主：
  - 主要 SDK 与最佳实践集中在 Python，对 TypeScript/Go 等语言支持相对有限；
  - 对于前后端统一编排的需求，需要额外设计跨语言层。
- 编排抽象不如 LangGraph 专注：
  - Workflow/Agent API 更像是在 RAG 框架上附加的功能，对「多 Agent 协作」「持久化状态」「前台/后台 Agent 生命周期」等关注相对较少；
  - 如果需要完整的「Agent Operating System」级别能力，往往仍需自研 Orchestrator 或引入 LangGraph 等专门框架。
- 可观测性与事件流需要自行建设：
  - 尽管可以通过日志/Hook 等方式接入，但没有像部分专门编排框架那样提供统一的事件流/可视化 UI，需要在 0004/0005 的观测与驭控层补充设计。

# 适用场景与使用方式建议

- 适用场景：
  - 以复杂 RAG（多数据源、多索引、多路由）为核心的问答/分析类 AIAgent；
  - 主要技术栈为 Python，团队已经在 LlamaIndex 上投入较多（索引、GraphRAG、评估等）。

- 使用方式建议：
  - 在「以数据为中心」的场景中，使用 LlamaIndex 统一管理索引与检索，再通过其 Agents/Workflow 实现局部编排；
  - 在「以流程编排为中心」的大型系统中，可考虑：
    - 用 LangGraph 或自研 orchestrator 表达顶层流程；
    - 用 LlamaIndex Agents/Workflow 作为「数据子流程引擎」，例如某些节点专门负责复杂检索与整合，再将结果返回上层编排。
