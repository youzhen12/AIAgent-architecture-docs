# QwenPaw

## 概述

QwenPaw 是一款由阿里巴巴 AgentScope AI 团队开源的**个人 AI 助手**（Personal AI Assistant）。它易于安装，支持本地或云端部署，可连接多种聊天渠道，并通过技能（Skills）机制轻松扩展能力。QwenPaw 前身为 CoPaw，于 2026 年 4 月 12 日正式更名为 QwenPaw，标志着其进入开源发展的新阶段。

核心理念：**Works for you, grows with you.**（为你工作，与你成长。）

## 基本信息

| 项目 | 详情 |
|------|------|
| GitHub 仓库 | [agentscope-ai/QwenPaw](https://github.com/agentscope-ai/QwenPaw) |
| 语言 | Python |
| 许可证 | Apache-2.0 |
| Stars | 16,000+ |
| Forks | 2,100+ |
| 创建时间 | 2026-02-24 |
| 最后推送 | 2026-04-17（昨天） |
| 最新 Release | v1.1.2（2026-04-17） |
| 文档 | [qwenpaw.agentscope.io](https://qwenpaw.agentscope.io/) |

## 核心特性

- **数据自主可控**：记忆和个性化完全由用户控制。本地部署数据留在本机，云端部署使用自选服务器。无第三方托管，无数据上传
- **技能扩展（Skills）**：内置日程管理、PDF/Office 处理、新闻摘要等技能；自定义技能自动加载，无锁定。技能决定了 QwenPaw 能做什么
- **多智能体协作**：创建多个独立 Agent，每个 Agent 拥有独立角色；启用协作技能实现 Agent 间通信，共同完成复杂任务
- **多层安全防护**：工具守卫、文件访问控制、技能安全扫描，确保安全运行
- **全渠道接入**：支持钉钉、飞书、微信、Discord、Telegram 等多种渠道。一个 QwenPaw，按需连接

## 典型应用场景

- **社交媒体**：每日热帖摘要（小红书、知乎、Reddit）、B站/YouTube 视频摘要
- **生产力**：邮件与新闻摘要推送到钉钉/飞书/QQ；邮件与日历联系人整理
- **创意与构建**：睡前描述目标自动执行，醒来看到原型；从选题到最终视频的完整工作流
- **研究与学习**：追踪科技与 AI 新闻，个人知识库搜索与复用
- **桌面与文件**：整理和搜索本地文件、阅读和总结文档、聊天中请求文件
- **探索更多**：将技能与定时任务组合成自己的 Agentic 应用

## 近期重要更新

- **2026-04-17**：v1.1.2 — 新增任务模式（`/mission`）用于自主多阶段任务执行；ACP 协议用于外部 Agent 委派；`qwenpaw doctor` 诊断命令；`qwenpaw agents create` CLI 创建 Agent；定时记忆整合（Dream）；新 Debug 页面
- **2026-04-14**：v1.1.1 发布
- **2026-04-12**：CoPaw 正式更名为 QwenPaw

## 快速开始

```bash
pip install qwenpaw
```

## 相关链接

- GitHub: https://github.com/agentscope-ai/QwenPaw
- 文档: https://qwenpaw.agentscope.io/
- 中文文档: https://github.com/agentscope-ai/QwenPaw/blob/main/README_zh.md
- PyPI: https://pypi.org/project/qwenpaw/
- Discord: https://discord.gg/eYMpfnkG8h
- 钉钉群: https://qr.dingtalk.com/action/joingroup?code=v1,k1,OmDlBXpjW+I2vWjKDsjvI9dhcXj
