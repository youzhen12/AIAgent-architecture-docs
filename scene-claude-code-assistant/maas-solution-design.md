---
title: Claude Code 启发的 Coding OS 场景端到端 MaaS 解决方案设计
status: draft
version: 0.1.0
owner: [负责人]
last_updated: YYYY-MM-DD
---

# 1. 客户业务目标与技术约束

- 业务目标：
  - 提升开发者效率（以完成任务数量/耗时衡量）；
  - 提高代码质量（以 bug 率、回滚率、测试覆盖率衡量）；
  - 让团队工程规范「可执行、可复用、可审计」；
  - 逐步将编码工作从「单 Agent 辅助」演进到「多 Agent 协作 + Harness 工程」的 Coding OS。
- 约束：
  - 代码仓库多为私有，需要强隐私保护，不允许明文源代码离开受控环境；
  - 开发者使用 IDE / CLI / TUI 等多种入口；
  - 团队希望保留对工具/权限/日志/审计的完全控制，不依赖黑盒 SaaS。

# 2. 场景与用例

- 场景：
  1. 代码探索与理解（Explore Agent）；
  2. 设计与规划（Plan Agent）；
  3. 实现与修改（General Purpose / Executor Agent）；
  4. 验证与对抗测试（Verification Agent）。

- 典型用例：
  - 快速理解一个复杂模块；
  - 根据需求描述完成修改（含生成代码 + 测试 + 文档草稿）；
  - 修复 bug 并验证不会破坏其他功能；
  - 对某类变更做「回归评估和总结」。

# 3. 模型与 MaaS 选型

- 访问方式：
  - 本地/私有环境自建 OpenAI 兼容网关；
  - 支持连接云模型和未来的本地开源模型（Llama、Qwen 等）。
- 模型选型：
  - 高复杂度任务（跨文件修改、大型 refactor）：GPT-5.x / Claude Sonnet；
  - 轻量任务（小修改、日志分析）：GPT-5.3 Instant / Qwen3.5-Plus；
  - Embedding：用于代码/文档/日志索引的 embedding 模型。

# 4. 工程链路设计

## 4.1 推理链路

- 入口：
  - CLI / IDE 插件 / MCP → Coordinator → Model Gateway。
- Prompt & Context：
  - 对标 Claude Code 的 prompts.ts 设计：静态前缀（身份、系统规则、工具使用规则、输出要求）+ 动态后缀（session guidance、memory、MCP instructions 等）；
  - 通过 `SYSTEM_PROMPT_DYNAMIC_BOUNDARY` 管理缓存边界；
  - 上下文包含：当前文件/目录、相关文件列表、历史对话摘要、任务描述等。

## 4.2 RAG

- 数据源：代码、文档、日志、历史变更；
- 向量库：
  - 小团队：Postgres + pgvector；
  - 大团队：Qdrant/Milvus；
- 用途：
  - 按语义检索代码片段和相关文档；
  - 检索类似变更或历史 bug 修复案例。

## 4.3 Agents 与 orchestrator

- Agents：
  - Explore / Plan / Verification / General Purpose 等；
- Orchestrator：
  - 自研 orchestrator（命令/事件层） + LangGraph 子流程（AgentTool → runAgent → query）；

## 4.4 工具与 Harness

- 工具：
  - FileRead/FileEdit/FileWrite、Glob/Grep、Git 工具、Test Runner、Lint、Build、Bash（受限）；
- Harness：
  - Tool Runtime：
    - 输入校验（schema + validateInput）；
    - PreToolUse / PostToolUse / Failure Hooks；
    - Permission 模型（allow/ask/deny + preventContinuation）；
  - 高危操作（文件删除、大规模重构、bash 写操作）必须 ask + 人审；
  - 对标 `harness-engineering.md` 的设计，将「好行为」编码进 Prompt 与 runtime。

# 5. 评估与测试

- Eval 维度：
  - 任务成功率（按「完成度 + 回归测试」判定）；
  - 代码质量（审查样本）；
  - 工具调用合规性（无越权/潜在破坏性操作）。
- 策略：
  - 使用一批标准任务（修改/bug 修复/新增功能）作为 Eval 集，定期回测；
  - 把 Verification Agent 的执行结果（测试命令 + 输出 + verdict）作为评估的一部分；
  - 对高风险仓库先在只读模式运行（Explore/Plan/Verification），收集数据后再开放写入能力。

# 6. 上线与部署

- 部署：
  - 本地单机模式（全部组件在开发者机器上）；
  - 远程工作区模式（Coordinator/工具服务远程，IDE/CLI 本地）。
- 灰度：
  - 从内部工具团队开始，逐步扩展到整个公司；
  - 写操作能力按团队和仓库逐步开通。

# 7. 风险与演进

- 风险：
  - 错误修改引入隐性问题；
  - 工具/权限配置不当导致高危操作被执行；
  - 过度依赖 Agent，开发者失去代码掌控感。
- 演进路径：
  - v1：只读 Explore/Plan/Verification；
  - v2：在严格 Harness 约束下提供写操作；
  - v3：引入更丰富的 Skill/Plugin/MCP 生态和团队级评估体系。
