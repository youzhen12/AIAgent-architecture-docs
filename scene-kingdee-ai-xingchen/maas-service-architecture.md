---
title: 金蝶 AI 星辰 MaaS / Agent 服务工程架构设计
status: draft
version: 0.1.0
owner: [待补充]
last_updated: 2026-04-03
scene_id: scene-kingdee-ai-xingchen
tags: [maas, service-architecture, api-design]
---

> 本文在 `maas-solution-design.md` 的基础上，进一步细化到工程实现层面，给出一个参考级的服务仓库架构、目录与关键文件职责以及对外 API 设计示例。
>
> 说明: 以下目录与文件名称为参考设计，可按实际技术栈调整 (例如 Java/Spring, Node.js/NestJS, Go 等)。

# 1. 服务整体架构概览

- 服务目标:
  - 对内为各业务云提供统一的模型服务 API 和 Agent 服务 API。
  - 对外为企业客户和 ISV 提供标准化的能力 API, 支持安全隔离和计费治理。
- 核心组件:
  - API 网关层: 统一域名、鉴权、限流、流量控制。
  - MaaS 应用服务: 提供 Model API 和 Agent API 的业务编排和路由。
  - 模型网关 (Model Gateway): 管理多模型路由、厂商接入和调用策略。
  - Agent 运行时: 注册、调度和执行财务、ESG、合同比审等业务 Agent。
  - 任务与队列服务: 支持长耗时异步任务 (报告生成、批量审核等)。
  - 数据与向量服务: 连接企业数据平台、向量数据库和知识库。
  - 观测与审计服务: 指标、日志、审计链路和调用成本统计。

# 2. 仓库目录结构设计

采用单仓多模块结构示例:

```text
kingdee-ai-xingchen-maas/
  api/
    openapi-model.yaml
    openapi-agent.yaml
    openapi-internal-admin.yaml

  apps/
    maas-api-service/
      src/
        main.
        routes/
          model-routes.
          agent-routes.
          task-routes.
          admin-routes.
        controllers/
          model-controller.
          agent-controller.
          task-controller.
          admin-controller.
      config/
        application.
        security.
        routing-models.
        quotas.

    model-gateway-service/
      src/
        main.
        adapters/
          provider-openai.
          provider-qwen.
          provider-internal-llm.
        services/
          model-routing-service.
          model-metrics-service.

    agent-runtime-service/
      src/
        main.
        agents/
          financial-analysis-agent.
          esg-report-agent.
          contract-review-agent.
        core/
          agent-registry.
          agent-orchestrator.

    task-service/
      src/
        main.
        task-dispatcher.
        task-worker.

  libs/
    domain/
      tenant-context.
      auth-models.
      agent-models.
      task-models.
    infra/
      http-client.
      mq-client.
      db-client.
      vector-client.
      logging.

  config/
    env-dev.
    env-staging.
    env-prod.

  scripts/
    deploy/...
    migrate/...

  docs/
    README.md
    runbook-maas.md
    runbook-agent-runtime.md
```

说明: 上面使用通用文件名后缀 main./controller./service. 等, 实际可按技术栈映射为 .java, .ts, .go 等。

# 3. 关键目录与文件职责

## 3.1 api 目录

- `api/openapi-model.yaml`
  - 描述 Model API 的接口契约, 例如 `/api/v1/model/chat`, `/api/v1/model/doc-analyze` 等。
  - 作为客户端 SDK 生成和接口对齐的唯一源头 (SSOT)。
- `api/openapi-agent.yaml`
  - 描述 Agent 服务 API 的接口契约, 例如 `/api/v1/agents/financial-analysis:run` 等。
- `api/openapi-internal-admin.yaml`
  - 描述内部管理接口 (能力目录、配额、路由策略等), 不对外开放。

## 3.2 apps/maas-api-service

- `src/main.`
  - 应用入口, 负责启动 Web 服务, 加载路由和中间件。
- `src/routes/model-routes.`
  - 注册所有 Model API 的 HTTP 路由, 将请求转发到对应 controller。
- `src/routes/agent-routes.`
  - 注册所有 Agent API 的 HTTP 路由。
- `src/routes/task-routes.`
  - 注册任务查询、取消等路由 (用于异步任务场景)。
- `src/routes/admin-routes.`
  - 注册内部管理路由 (能力目录、配额、限流策略等)。

- `src/controllers/model-controller.`
  - 实现 Model API 的入参校验、认证鉴权、调用上下文构造和结果包装。
  - 调用 `libs/domain` 中的模型服务抽象, 不直接访问模型厂商 SDK。
- `src/controllers/agent-controller.`
  - 实现 Agent API 的入参校验和调用 orchestration 层。
  - 根据同步/异步模式, 决定直接返回结果或创建任务。
- `src/controllers/task-controller.`
  - 提供任务状态查询接口, 用于 ESG 报告等长耗时任务。
- `src/controllers/admin-controller.`
  - 提供内部接口用于维护能力配置、模型路由策略和配额规则。

- `config/application.`
  - 应用通用配置 (端口、日志级别、服务发现等)。
- `config/security.`
  - 安全相关配置 (OAuth2/JWT、公钥路径、API Key 验证策略等)。
- `config/routing-models.`
  - 模型路由规则配置, 定义不同场景使用哪一类模型 (通用模型、表格模型、法务模型等)。
- `config/quotas.`
  - 各租户/各能力的配额与限流策略配置。

## 3.3 apps/model-gateway-service

- `src/main.`
  - 模型网关服务入口, 提供统一的模型调用接口给 MaaS 应用服务。
- `src/adapters/provider-openai.`
  - 对接某厂商模型的适配器, 封装鉴权、重试、错误码转换等。
- `src/adapters/provider-qwen.`
  - 对接另一家模型供应商的适配器。
- `src/adapters/provider-internal-llm.`
  - 对接自研或专有云部署模型的适配器。
- `src/services/model-routing-service.`
  - 根据调用场景、租户、成本策略等, 决定调用哪一个模型提供方。
- `src/services/model-metrics-service.`
  - 收集模型调用指标 (延迟、成功率、成本等), 回报到观测系统。

## 3.4 apps/agent-runtime-service

- `src/main.`
  - Agent 运行时服务入口, 承载业务 Agent 的执行与编排逻辑。
- `src/agents/financial-analysis-agent.`
  - 财务智能分析 Agent 的实现, 封装财务指标计算、报表分析 Prompt 模版、结果结构化输出等。
- `src/agents/esg-report-agent.`
  - ESG 报告 Agent 的实现, 封装 ESG 模板、数据抽取与写作链路。
- `src/agents/contract-review-agent.`
  - 合同比审 Agent 的实现, 封装合同解析、风险条款识别和建议生成逻辑。
- `src/core/agent-registry.`
  - 维护 Agent 能力目录, 支持按 ID 或能力名检索 Agent 实例。
- `src/core/agent-orchestrator.`
  - 负责 Agent 的调用编排, 包括多 Agent 协同、任务拆分与结果聚合。

## 3.5 apps/task-service

- `src/main.`
  - 任务服务入口, 提供统一任务管理接口。
- `src/task-dispatcher.`
  - 接收来自 MaaS 应用服务的异步任务请求, 投递到队列或调度系统。
- `src/task-worker.`
  - 从队列中拉取任务, 调用 agent-runtime-service 执行, 持久化结果并更新任务状态。

## 3.6 libs/domain 与 libs/infra

- `libs/domain/tenant-context.`
  - 租户上下文模型与解析逻辑, 用于在各服务间携带租户信息和权限信息。
- `libs/domain/auth-models.`
  - 鉴权相关领域模型 (客户端、应用、角色、权限等)。
- `libs/domain/agent-models.`
  - Agent 请求与响应的通用结构定义, 包含 traceId、引用来源列表等。
- `libs/domain/task-models.`
  - 异步任务的状态机与任务元数据结构定义。

- `libs/infra/http-client.`
  - 封装对外 HTTP 请求逻辑, 包括重试、超时和统一错误处理。
- `libs/infra/mq-client.`
  - 消息队列客户端封装, 统一任务和事件的发布与订阅。
- `libs/infra/db-client.`
  - 数据库访问封装, 用于任务、配额和能力配置等表的持久化。
- `libs/infra/vector-client.`
  - 向量数据库客户端封装, 用于 RAG 检索等场景。
- `libs/infra/logging.`
  - 日志与追踪工具封装, 对接统一观测平台。

## 3.7 config 与 scripts

- `config/env-dev`, `config/env-staging`, `config/env-prod`
  - 各环境的配置覆盖, 如数据库连接、MQ 地址、模型服务 endpoint 等。
- `scripts/deploy/`
  - 部署脚本, 如容器构建、Helm Chart 模版或 CI/CD 流程脚本。
- `scripts/migrate/`
  - 数据库迁移脚本, 定义任务表、配额表、调用日志表等结构。

# 4. 对外 API 设计示例

本节基于 `maas-solution-design.md` 中的 Model API 和 Agent API 描述, 给出一组更具体的接口定义示例。

## 4.1 公共字段约定

- 所有请求都包含以下通用字段:
  - `tenant_id`: 租户标识。
  - `app_id`: 调用应用标识。
  - `scene_id`: 调用场景标识, 例如 `scene-kingdee-ai-xingchen` 或业务子场景。
  - `request_id`: 调用方生成的请求 ID, 用于幂等和追踪。

- 所有响应都包含以下通用字段:
  - `request_id`: 与请求中一致。
  - `trace_id`: 服务端生成的链路追踪 ID。
  - `code`: 业务状态码 (0 为成功, 其他为错误码)。
  - `message`: 对 code 的文本说明。

## 4.2 Model API 示例

1. 对话模型接口

- 方法: `POST`
- 路径: `/api/v1/model/chat`
- 用途: 通用对话与问答, 可用于管理助手或嵌入业务前端。

请求体示例字段:

```json
{
  "tenant_id": "t_kingdee_demo",
  "app_id": "finance-portal",
  "scene_id": "mgmt-chat",
  "request_id": "uuid-123",
  "messages": [
    {"role": "system", "content": "你是集团 CFO 的智能助手"},
    {"role": "user", "content": "帮我分析下本季度毛利率下降的原因"}
  ],
  "options": {
    "max_tokens": 1024,
    "temperature": 0.2
  }
}
```

响应体示例字段:

```json
{
  "request_id": "uuid-123",
  "trace_id": "trace-abc",
  "code": 0,
  "message": "ok",
  "data": {
    "output_text": "分析结论...",
    "usage": {"prompt_tokens": 200, "completion_tokens": 400},
    "model": "qwen-finance-001"
  }
}
```

2. 文档解析模型接口

- 方法: `POST`
- 路径: `/api/v1/model/doc-analyze`
- 用途: 对合同、报告等文档进行解析和结构化抽取。

请求体关键字段:

- `file_id` 或 `file_content_base64`: 文档标识或内容。
- `doc_type`: 文档类型, 如 `contract`, `esg_report` 等。

3. 表格理解模型接口

- 方法: `POST`
- 路径: `/api/v1/model/table-analyze`
- 用途: 对上传的表格或报表进行结构化解析和简单数值分析。

请求体关键字段:

- `table_source`: 表格数据来源 (上传文件、数据表 ID 等)。
- `analysis_query`: 用户在表格上的分析意图描述。

## 4.3 Agent API 示例

1. 财务智能分析 Agent

- 方法: `POST`
- 路径: `/api/v1/agents/financial-analysis:run`

请求体示例字段:

```json
{
  "tenant_id": "t_kingdee_demo",
  "app_id": "finance-portal",
  "scene_id": "finance-quarter-analysis",
  "request_id": "uuid-234",
  "query": "分析本季度与去年同期的收入和毛利率变化, 指出前三个主要原因",
  "parameters": {
    "period": "2025Q4",
    "compare_to": "2024Q4",
    "org_scope": ["group", "subsidiary_a"]
  }
}
```

响应体关键字段:

- `analysis_report`: 结构化分析结果 (包括重点结论、图表数据、分项说明等)。
- `references`: 本次分析引用的数据表、报表和历史报告列表。

2. ESG 报告 Agent

- 方法: `POST`
- 路径: `/api/v1/agents/esg-report:run`
- 调用模式: 优先采用异步任务 (生成报告耗时较长)。

请求体关键字段:

- `report_period`: 报告期。
- `template_id`: ESG 模板标识。

响应体关键字段:

- `task_id`: 异步任务 ID, 调用方通过任务查询接口获取结果。

3. 合同比审 Agent

- 方法: `POST`
- 路径: `/api/v1/agents/contract-review:run`

请求体关键字段:

- `contract_file_id`: 合同文件标识。
- `review_focus`: 关注点, 如 `liability`, `payment_terms`, `termination` 等。

响应体关键字段:

- `risk_items`: 风险条款列表, 包含条款位置、风险级别和说明。
- `rewrite_suggestions`: 修改建议文本和推荐条款示例。

## 4.4 任务查询 API 示例

- 方法: `GET`
- 路径: `/api/v1/tasks/{task_id}`
- 用途: 查询异步任务 (如 ESG 报告生成) 的状态和结果。

响应体关键字段:

- `status`: 任务状态, 如 `pending`, `running`, `succeeded`, `failed`。
- `result`: 当状态为 `succeeded` 时, 包含结构化结果数据; 对于 ESG 报告, 可返回报告内容 ID 或下载链接。

# 5. 小结与后续工作

- 本文给出了一个针对金蝶 AI 星辰 MaaS / Agent 服务的参考工程架构, 细化了仓库目录、关键文件和 API 示意。
- 实际落地时, 建议:
  - 按实际技术栈和组织结构调整服务拆分与目录设计。
  - 将本文件中的 API 示例沉淀为正式 OpenAPI 文档 (更新 `api/` 目录)。
  - 将关键设计决策上升为 `aia-agent-architecture/decisions/` 下的 ADR, 便于不同场景共享经验。
