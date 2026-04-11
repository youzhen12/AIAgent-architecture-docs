---
title: "AIAgent 总体架构概览"
status: draft
version: 0.1.0
owner: [待定]
last_updated: 2026-04-02
---

# 1. 文档目的

- 作为整个 `docs/aia-agent-architecture/` 目录的「导航图」和总览说明；
- 给出从模型层到场景应用层的 AIAgent 整体分层视图；
- 串联各类决策（`decisions/000x-*`）、模板（`templates/`）与场景文档（`scene-*/`）。

# 2. 目录结构约定

- `decisions/`：按编号组织的技术选型与工程决策；
- `overview.md`：本文件，整体架构与目录导航；
- `templates/`：通用模板（架构文档、解决方案设计等）；
- `research/`（预留）：调研类长文，可根据需要补充。

# 3. AIAgent 整体架构分层

从下到上，可以粗略分为以下几个层次：

1. 模型与推理层：
   - 各类大模型（对话、代码、多模态等）及其推理服务 / 网关；
   - 关注模型能力、成本、上下文长度、多模型路由等。
2. 知识与数据层：
   - 结构化数据、文档、代码库、日志等知识来源；
   - 向量数据库 / 检索服务（RAG、树状索引、Web 搜索等）。
3. Agent 编排与运行时层：
   - Orchestrator / Agent Runtime（单 Agent、多 Agent、子流程）；
   - 工具运行时与权限模型（Tool Sandbox / Harness）。
4. 观测与评估层：
   - 日志、指标、追踪（Observability）；
   - Eval / 回归测试 / 在线指标 / 人审抽检等测试与评估机制。
5. 场景与应用层：
   - 具体业务场景（IDE 助手、客服、SaaS 平台 Agent 等）；
   - 每个场景的业务流程、用户旅程与非功能性要求。

`decisions/0001-0010` 这组 ADR 分别锚定在上述不同层次上，用于指导不同场景在同一技术维度下做出可比较的决策。

# 4. 决策文档组织方式

- 目录结构：
  - `decisions/000x-some-topic/`：
    - `README.md`：该主题的选型维度与注意事项；
    - 其它 `*.md`：针对单个方案的调研与评估（例如 `qdrant.md`、`langgraph.md` 等）。
  - `decisions/000x-some-topic.md`：顶层 ADR，记录本项目在该主题上的当前决策快照。

- 使用建议：
  - 想了解「有哪些可选方案、怎么对比」：先读目录下的 `README.md` 和子文档；
  - 想知道「本项目当前选了什么、为什么」：读对应的 `000x-*.md` 顶层 ADR。

# 4.1 决策主题一览（0001–0010）

- `0001-vector-db-selection`：向量数据库与基础检索方案选型；
- `0002-agent-orchestration-framework-selection`：Agent 编排框架与自研 Orchestrator 的边界划分；
- `0003-model-and-inference-service-selection`：模型访问方式与推理服务形态（直连厂商 / 网关 / 自建集群等）；
- `0004-observability-and-logging-selection`：日志、指标、追踪与观测平台选型；
- `0005-engineering-design`：Prompt / Context / Harness Engineering 等工程设计主线；
- `0006-foundation-models-selection`：大模型厂商与版本选型；
- `0007-agent-testing-strategy-selection`：Agent 测试与评估策略（Eval / 回归 / 在线指标等）；
- `0008-knowledge-store-and-retrieval-selection`：知识存储与检索方式（全文检索、向量检索、树状索引、Web 搜索等）；
- `0009-model-finetuning-selection`：模型微调策略（是否需要微调、何时用托管微调 / 自托管 PEFT 等）；
- `0010-tool-sandbox-and-execution-control`：工具沙盒与执行控制（权限、隔离、审计与审批机制）。

# 5. 与场景文档的关系

- 每个具体场景（`docs/scene-*/architecture-overview.md`）在「核心技术选型」章节中：
  - 给出该场景下的实际选型结论；
  - 同时引用 `decisions/000x-*` 作为通用对比材料。

- 当场景与全局 ADR 不一致时，需在场景文档中说明「偏离原因」和「风险评估」。
