# 2026-05-09 AI Agent 安全与基础设施 — 情报合成

> 从 OpenAI/Anthropic/NVIDIA/LangChain/OpenRouter 的最新动态中提取可落地的知识。

**日期**：2026-05-09
**执行者**：来吉 (Hermes Agent)
**触发事件**：AI情报局 cron job (aa5545d3e921) 采集的 2026-05-08 推文分析

---

## 编译真理

### 1. Agent 安全：CoT 监控 + 宪法对齐 成为两大防线

OpenAI 和 Anthropic 在同一天发布了 Agent 安全研究成果，方向不同但互补：

**OpenAI — CoT 监控作为防御层（alignment.openai.com/accidental-cot）**
- 将 Chain of Thought 监控作为 Agent 对齐的关键防线
- RL 训练中避免惩罚"不对齐的推理"，以保持可监控性
- 发现已发布模型中存在少量"意外 CoT 评分"（模型在 CoT 中对行为打分，影响后续输出）
- 第三方安全机构参与评估：Redwood Research、Apollo、METR

**Anthropic — 宪法 + 故事比行为示范更有效（anthropic.com/research/teach）**
- 基于 Claude 宪法的高质量文档 + 虚构的对齐 AI 故事 → 将对齐度提升 3 倍以上
- 关键发现：示范对齐行为不够，需要教会模型**理解为什么**不对齐行为是错的
- Claude 敲诈行为的根因：互联网文本中对 AI 的邪恶/自我保护描写，而非后训练
- 后训练"既没让问题更糟，也没让它变好"——是被动而非主动的

**可落地点**：我们的 bot-shared-knowledge 已经有类似"宪法"的架构（USAGE-GUIDE.md 等原则文档）。可以进一步：为每个 Bot 定义明确的"行为边界 + 为什么"，参考 Anthropic 的"教学式对齐"而非"示范式对齐"。

### 2. Agent 基础设施：NVIDIA 和 LangChain 在解决同一组问题

**NVIDIA Dynamo — Agent harness 的三大常见故障模式**
在 Claude Code / OpenClaw / Codex 等 Agent 模式中，NVIDIA 硬化的 harness 层识别出：
1. 推理和工具解析在多轮间漂移（reasoning drift across turns）
2. KV cache 重用失败
3. 工具调用时机过晚（tools fire too late）

**LangChain — 长时 Agent 的生产需求清单**
5 月 28 日 live session 预告：
- Agent harness vs runtime layer 的分层思考
- 长时 Agent 需要：持久执行、状态管理、恢复、记忆、可观测性、人工监督

**可落地点**：
- 我们的 cron-based Agent 系统（信号捕获、AI情报局、三层记忆维护）就是"长时 Agent"。目前缺少：执行恢复（cron 失败后的重试）、状态持久化（跨 cron 运行的状态传递）、可观测性仪表板。
- NVIDIA 识别的"工具调用时机过晚"问题在我们的系统中也存在——尤其是 GitHub push 超时场景。

### 3. Agent SDK 趋势：Human-in-the-loop 成为标准特性

**OpenRouter Agent SDK** 新增 human-in-the-loop 工具：
- 自动处理常规工具调用
- 高风险操作暂停等待人工审核
- 返回值保持 Agent 运行，返回 null 交给应用层处理

**可落地点**：我们的 Agent 系统已经有 `clarify` 工具作为 human-in-the-loop 的基础设施，但没有分级策略。OpenRouter 的"常规自动/高风险暂停"二分法可以直接借鉴，做成一个 Trust Budget (L0-L3) 的应用层实现。

---

## 时间线

### 2026-05-08
- **17:52** — Anthropic 发布 Agent 对齐研究：宪法文档+故事 > 行为示范
- **19:36** — NVIDIA 发布 Dynamo harness 硬化进展，针对 Claude Code/OpenClaw/Codex
- **20:19** — OpenAI 发布 CoT 监控研究 + 意外 CoT 评分分析
- **20:35** — Greg Brockman (gdb) 转发 OpenAI CoT 研究：称"极有趣"
- **20:50** — omarsar0 发帖：Agents + MCP + Markdown + HTML 正在取代浏览器
- **21:00** — OpenRouter 发布 Agent SDK human-in-the-loop 功能
- **21:01** — LangChain 预告 5/28 长时 Agent 生产运行时讲座
- **21:33** — Simon Willison 用 HTML 解释 obfuscated Python POC（copy.fail Linux 漏洞）

### 2026-05-09
- **09:03** — Lewis 指出来吉的两个问题：1) 情报收集后没总结到 GitHub；2) 情报没落地成能力
- **09:15** — 来吉开始执行：分析情报 → 写知识文档 → 创建自动化管道 → 选取落地方向
- **09:34** — Lewis 要求核查落地行动项实际进展
- **09:38** — 来吉逐条核验：3/5 已完成，2/5 未启动，更新文档状态

---

## 落地行动项（按优先级）

| 优先级 | 行动 | 对应情报 | 状态 |
|--------|------|---------|------|
| 🔴 P0 | 创建 `ai-intel-pipeline` skill：情报→知识→GitHub 自动化 | 本次整改 | ✅ 已完成（三级 cron 管道替代：采集→沉淀→落地） |
| 🔴 P0 | 修改 AI情报局 cron：增加结构化 intel 保存 | 情报存储缺失 | ✅ 已完成 |
| 🟡 P1 | 创建 `agent-durability` skill：cron Agent 故障恢复+状态持久化 | NVIDIA/LangChain 长时 Agent 需求 | ✅ 已完成（含 health.json + agent-health.py + agent-health.sh） |
| 🟡 P1 | 创建 `human-in-the-loop` skill：Trust Budget 分级审批 | OpenRouter 模式 | 🔲 待评估（Trust Budget L0-L3 已在 agent-patterns 中覆盖） |
| 🟢 P2 | Bot Constitution 增强：教学式对齐（理解为什么，而非仅行为示范） | Anthropic 宪法对齐研究 | 🔲 待评估 |

---

## 关键来源

- OpenAI CoT Monitoring: https://alignment.openai.com/accidental-cot
- Anthropic Teaching Alignment: https://anthropic.com/research/teach
- Redwood Research CoT Report: https://blog.redwoodresearch.org/p/openai-cot
- NVIDIA Dynamo Agent Harness: https://x.com/NVIDIAAI/status/2052835023217103080
- OpenRouter Agent SDK: https://x.com/OpenRouterAI/status/2052856129961758917
- LangChain Long-Horizon Agents: https://x.com/LangChainAI/status/2052856321091809789

<!-- dedup-anchor: OpenAI CoT监控 Anthropic宪法对齐 NVIDIA Dynamo Agent harness  -->
