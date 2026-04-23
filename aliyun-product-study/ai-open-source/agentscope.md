# AgentScope

## 概述

AgentScope 是一款由阿里巴巴通义实验室（AgentScope AI 团队）开源的企业级智能体框架，定位为"开箱即用、生产就绪"的 Agent 开发平台。其核心理念是释放 LLM 的推理与工具调用能力，而非用僵化的提示工程和预设流程束缚模型。

## 基本信息

| 项目 | 详情 |
|------|------|
| GitHub 仓库 | [agentscope-ai/agentscope](https://github.com/agentscope-ai/agentscope) |
| 语言 | Python |
| 许可证 | Apache-2.0 |
| Stars | 24,000+ |
| Forks | 2,600+ |
| 最新 Release | v1.0.18（2026-03-26） |
| 最后推送 | 2026-04-16（3 天前） |
| 文档 | [doc.agentscope.io](https://doc.agentscope.io/) |
| 论文 | [arXiv:2402.14034](https://arxiv.org/abs/2402.14034) |

## 核心特性

- **简单易用**：内置 ReAct 智能体、工具、技能（Skills）、人机协作、记忆、规划、实时语音、评估和模型微调，5 分钟即可开始构建智能体应用
- **高度可扩展**：大量生态系统集成（工具、记忆、可观测性），内置 MCP（Model Context Protocol）和 A2A（Agent-to-Agent）协议支持，MsgHub 提供灵活的多智能体编排能力
- **生产就绪**：支持本地部署、云端 Serverless 部署和 K8s 集群部署，内置 OpenTelemetry 可观测性支持
- **模型微调**：原生支持模型微调，集成 Trinity-RFT 库实现 Agentic RL

## 近期重要更新

- **2026-04**：把 2.0 路线图公布
- **2026-02**：实时语音 Agent（Realtime Voice Agent）支持
- **2026-01**：数据库支持与记忆模块压缩
- **2025-12**：A2A（Agent-to-Agent）协议支持
- **2025-12**：TTS（文本转语音）支持
- **2025-11**：Anthropic Agent Skill 支持、Alias-Agent、Data-Juicer Agent、Agentic RL（Trinity-RFT）、ReMe 长期记忆增强

## 应用场景

- 构建单智能体和多智能体应用
- 企业级 Agent 平台开发
- 模型微调与强化学习训练
- 实时语音交互场景
- 需要 MCP/A2A 协议集成的场景

## 快速开始

```bash
pip install agentscope
```

## 相关链接

- GitHub: https://github.com/agentscope-ai/agentscope
- 文档: https://doc.agentscope.io/
- 中文文档: https://doc.agentscope.io/zh_CN/
- Discord: https://discord.gg/eYMpfnkG8h
- 2.0 路线图: https://github.com/orgs/agentscope-ai/projects/2
