---
title: 行业场景与文档映射总览
status: draft
version: 0.1.0
owner: [负责人]
last_updated: YYYY-MM-DD
---

## 1. 场景分组总览

- SaaS / 企业软件：
  - SaaS 平台内嵌 Agent / 管理后台助手：
    - 架构：`scene-saas-platform-agent/architecture-overview.md`
    - AI 需求与价值：`scene-saas-platform-agent/ai-application-overview.md`
    - MaaS 方案：`scene-saas-platform-agent/maas-solution-design.md`

- 客服与服务云：
  - 智能客服 / 联络中心：
    - 架构：`scene-customer-support/architecture-overview.md`
    - AI 需求与价值：`scene-customer-support/ai-application-overview.md`
    - MaaS 方案：`scene-customer-support/maas-solution-design.md`

- 零售 & 电商：
  - 秒杀 / 大促旁路 AIAgent：
    - 架构：`scene-flash-sale-platform/architecture-overview.md`
    - AI 需求与价值：`scene-flash-sale-platform/ai-application-overview.md`
    - MaaS 方案：`scene-flash-sale-platform/maas-solution-design.md`

- 开发者工具 / AI 原生工具链：
  - Claude Code 风格 Coding OS：
    - 架构：`scene-claude-code-assistant/architecture-overview.md`
    - AI 需求与价值：`scene-claude-code-assistant/ai-application-overview.md`
    - MaaS 方案：`scene-claude-code-assistant/maas-solution-design.md`

- 个人助手与学习场景：
  - 个人助理 / 工作流助手：
    - 架构：`scene-personal-assistant/architecture-overview.md`
    - AI 需求与价值：`scene-personal-assistant/ai-application-overview.md`
  - 初学者 AIAgent Playground：
    - 架构：`scene-beginner-playground/architecture-overview.md`
    - AI 需求与价值：`scene-beginner-playground/ai-application-overview.md`

> 后续如需扩展到金融、医疗、制造等垂直行业，可以在这里新增分组，并在 `scene-*/` 目录中补充对应架构与方案文档。

## 2. 通用决策与工程主线

- 通用决策目录（与所有场景共享）：
  - 向量数据库选型：`aia-agent-architecture/decisions/0001-vector-db-selection/`
  - Agent 编排框架选型：`aia-agent-architecture/decisions/0002-agent-orchestration-framework-selection/`
  - 模型与推理服务选型：`aia-agent-architecture/decisions/0003-model-and-inference-service-selection/`
  - 可观测性与日志方案选型：`aia-agent-architecture/decisions/0004-observability-and-logging-selection/`
  - 工程设计（Prompt/Context/Harness）：`aia-agent-architecture/decisions/0005-engineering-design/`
  - 大模型厂商与版本选型：`aia-agent-architecture/decisions/0006-foundation-models-selection/`
  - Agent 测试与评估方案选型：`aia-agent-architecture/decisions/0007-agent-testing-strategy-selection/`

## 3. 按 JD 视角的映射

- 「深入研究不同行业SaaS/AI原生软件」：
  - SaaS 平台 / 客服 / 秒杀 / Coding OS ⇒ 对应以上场景分组。
- 「梳理各场景 AI 需求和价值」：
  - 对应 `scene-*/ai-application-overview.md`。
- 「端到端 MaaS 解决方案」：
  - 对应 `scene-*/maas-solution-design.md` + 本目录后续的 POC 模板和案例文档。
