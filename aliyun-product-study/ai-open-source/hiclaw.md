# HiClaw

## 概述

HiClaw 是一款由阿里巴巴 AgentScope AI 团队开源的**协作式多智能体操作系统**（Collaborative Multi-Agent OS）。它通过 Matrix 协议房间实现多 Agent 之间的透明协作，支持人类在整个过程中进行可视化和干预。HiClaw 采用 Manager-Workers 架构，Manager 负责集中编排多个 Worker，专注于企业环境中人与 Agent 以及 Agent 之间的协作场景。

HiClaw 不与其他 xxClaw 项目竞争——它不实现 Agent 逻辑本身，而是编排和管理多个 Agent 容器（包括 Manager 和众多 Worker）。

## 基本信息

| 项目 | 详情 |
|------|------|
| GitHub 仓库 | [agentscope-ai/HiClaw](https://github.com/agentscope-ai/HiClaw) |
| 语言 | Shell |
| 许可证 | Apache-2.0 |
| Stars | 4,200+ |
| Forks | 477+ |
| 创建时间 | 2026-02-21 |
| 最后推送 | 2026-04-18（刚刚） |
| 官网 | [hiclaw.io](https://hiclaw.io) |

## 核心特性

- **Manager-Workers 架构**：通过 Agent 管理 Agent，消除人类对单个 Worker 的监督需求
- **可定制 Agent**：每个 Agent 支持灵活配置，包括 OpenClaw、Copaw、NanoClaw、ZeroClaw 以及企业自建 Agent；提供 Worker 和 Team 模板市场
- **MinIO 共享文件系统**：引入共享文件系统用于 Agent 间信息交换，显著降低多 Agent 协作场景中的 Token 消耗
- **Higress AI 网关**：集中流量管理，消除凭证相关风险，Worker 仅使用消费令牌，真实凭证（API Key、GitHub PAT 等）保留在网关中
- **Element IM 客户端 + Tuwunel IM 服务器**：基于 Matrix 协议，消除钉钉/飞书集成开销和企业审批流程，支持快速用户入驻

## 安全与隐私

- **企业级安全**：Worker Agent 仅使用消费令牌操作，真实凭证不暴露给 Worker
- **完全私有化**：Matrix 是去中心化的开放协议，可自托管，无供应商锁定，无数据采集
- **默认 Human-in-the-Loop**：每个 Matrix 房间都包含人类用户、Manager 和 Worker，确保全程可控

## 近期重要更新

- **2026-04-14**：HiClaw 作为 Kubernetes 原生多智能体协作编排系统的深度解析
- **2026-04-03**：v1.0.9 — 引入 Kubernetes 风格声明式资源管理（YAML 定义 Worker/Team/Human）、Worker 模板市场、Manager CoPaw 运行时、Nacos Skills Registry
- **2026-03-14**：v1.0.6 — 企业级 MCP Server 管理，零凭证暴露
- **2026-03-10**：v1.0.4 — CoPaw Worker 支持，内存占用降低 80%
- **2026-03-04**：HiClaw 正式开源

## 应用场景

- 企业级多 Agent 协作编排
- 需要人类监督的 Agent 团队任务
- 去中心化 IM 环境中的 Agent 服务
- 需要集中管理多个 Agent 容器的场景

## 相关链接

- GitHub: https://github.com/agentscope-ai/HiClaw
- 官网: https://hiclaw.io
- Discord: https://discord.com/invite/NVjNA4BAVw
- DeepWiki: https://deepwiki.com/higress-group/hiclaw
