---
title: 智能客服场景 AI MaaS 案例草稿
status: draft
version: 0.1.0
owner: [负责人]
last_updated: YYYY-MM-DD
---

## 1. 背景与挑战

- 客户类型：ToC/ToB 混合型客服体系，覆盖 Web/App/IM/电话多个渠道。
- 痛点：
  - 人工成本持续攀升，业务高峰时期排队严重；
  - FAQ 与知识库维护困难，知识更新不及时；
  - 服务质量不稳定，质检覆盖率低。

## 2. 解决方案概述

- 引入大模型驱动的智能客服系统：
  - FAQ 自助问答；
  - 工单/订单/设备状态查询；
  - 升级单生成与总结；
  - 自动质检建议。
- 技术基座：
  - 阿里云 Qwen 模型 + 百炼 / Agent 平台；
  - 向量检索 + 多 Agent（Router/FAQ/Task/Escalation/QC）工作流。

## 3. 技术架构亮点

- 架构参见：`scene-customer-support/architecture-overview.md`
- 亮点：
  - 渠道接入统一由客服网关处理，AI 作为「增强层」；
  - 对客服系统、CRM、订单系统进行工具化封装，统一由 Agent 调用；
  - 通过决策与日志约束高风险回答与操作。

## 4. 效果与价值（示意）

- 自助解决率提升 20–30pp；
- AHT 降低 15–25%；
- 质检覆盖率显著提高，助力优化话术与产品体验。
