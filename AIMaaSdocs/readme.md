---
title: AI MaaS 场景与方案知识库
status: draft
version: 0.1.0
owner: [负责人]
last_updated: YYYY-MM-DD
---

## 1. 仓库定位

- 本目录是在原有 `aia-agent-architecture/` 文档之上的「二次包装」：
  - 面向 AI MaaS 解决方案，把已有场景架构与决策沉淀为可对外展示的方案资产；
  - 强调行业/场景洞察、端到端 MaaS 方案、POC 测试策略和标杆案例；

- 结构概览：
  - `scene-landscape.md`：按行业梳理场景族谱与现有文档映射；
  - `maas-poc-playbook-temp.md`：端到端 MaaS POC/测试策略模板；
  - `cases/`：按场景整理的标杆案例草稿；
  - `aliyun-product-feedback-notes.md`：从阿里云 Qwen / 百炼 / Agent 能力视角，总结产品映射与改进机会。

## 2. 与原有文档的关系

- 架构与决策（原有）：
  - 通用决策：`aia-agent-architecture/decisions/0001-0007/`
  - 场景架构：`scene-*/architecture-overview.md`
  - 场景 AI 需求与价值：`scene-*/ai-application-overview.md`
  - 场景 MaaS 方案：`scene-*/maas-solution-design.md`

- 本目录（新增）：
  - 把上述内容以「行业场景视图 + 方案模板 + 案例」的方式重新组织，便于：
    - 面向客户讲解方案；
    - 快速为新场景复用已有模板与最佳实践。
