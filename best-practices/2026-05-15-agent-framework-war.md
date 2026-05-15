# 2026-05-15 Agent 框架军备竞赛：平台化 + 开源化双线爆发

> 一日之内 7 条 Agent 框架级发布：LangChain Interrupt 全栈平台、Codex Hooks、Claude 免费额度、Ollama 原生 Codex、Grok Build CLI、Copilot 桌面版——Agent 工程正从"自己写 loop"转向"选托管平台"，同时开源模型驱动 Agent 的链路也已打通。

**日期**：2026-05-15
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：7 条 Agent 框架相关情报集中爆发

---

## 编译真理

### 1. Agent 工程范式迁移：从自建基础设施 → 托管平台

LangChain Interrupt 全栈平台（Smith Engine / SmithDB / Sandboxes / Managed Deep Agents / LLM Gateway / Context Hub）标志着 Agent 工程进入"平台化"阶段。不再是每个团队自己实现 Agent loop + context 管理 + sandbox 执行，而是托管平台提供开箱即用的全套基础设施。

**关键洞察**：选型时优先评估平台成熟度（LangSmith / Codex Platform / Grok Build），而非从零写 Agent loop。平台竞争正在从"单个能力"升级为"全栈覆盖"。

### 2. Codex 领跑企业级：Hooks 机制重新定义 Agent 可控性

OpenAI Codex Hooks 允许在任务循环关键节点插入自定义脚本（validator / secret 扫描 / 审计日志 / 按目录定制行为），Programmatic Access Tokens 支持 CI/CD 集成。这是 Agent 从"黑盒执行"到"企业级可审计"的关键一步。

**关键洞察**：Hooks 是 Agent 安全合规的基础设施级能力。Hermes 的 cron Agent 应在工具调用前/输出后插入校验脚本 + 自动记忆持久化。

### 3. 开源 Agent 链路已闭环：Ollama + Codex 消除 API 依赖

Ollama 0.24 原生支持 Codex app，kimi-k2.6 / glm-5.1 / nemotron-3 / gemma4 / qwen3.6 等开源模型可直接驱动 Codex sandbox 执行和审批流，视觉模型配截图回传闭环前端排查。

**关键洞察**：Hermes 的 Codex Agent 可切换为本地开源模型推理链路——数据不出境、不受 API 封号影响、模型切换零成本。这是去风险的关键架构选项。

### 4. 三足鼎立：Codex vs Claude Code vs Grok Build

| 维度 | Codex (OpenAI) | Claude Code (Anthropic) | Grok Build (xAI) |
|------|---------------|------------------------|-------------------|
| 差异化 | Hooks 机制 + 移动端 + 企业级审计 | 免费额度降成本 + 长上下文推理 | Subagents 并行 8 个 + Skills 模块化 + Plugins 市场 |
| 模型底座 | GPT 系列 | Claude 系列 | grok-code-fast-1 (SWE-Bench 70.8%, 256K) |
| 战略 | 平台锁定 + 企业合规 | 入口补贴 + 开发者生态 | 开源式生态 + 模块化扩展 |

**关键洞察**：三者差异化路径清晰——Codex 走企业合规 + 全栈平台，Claude 走开发者入口补贴，Grok 走模块化扩展市场。不押单一平台，按任务类型路由。

### 5. Copilot 桌面版的威胁信号

GitHub Copilot 独立桌面应用（非编辑器插件），原生整合 PR / Issue / Actions。这意味着 GitHub 正在构建从代码托管到 Agent 执行的一体化闭环——对所有第三方 Agent 工具构成平台级威胁。

**关键洞察**：跟踪 Copilot 桌面版进展，评估其对 Codex / Claude Code 工作流的替代可能性。GitHub 拥有"代码仓库 + CI + Agent"三位一体的天然优势。

---

## 时间线

### 2026-05-15

- **03:45** — LangChain 发布 Interrupt 全栈 Agent 平台（LangSmith Engine / SmithDB / Sandboxes / Managed Deep Agents / LLM Gateway / Context Hub / Deep Agents 0.6）[来源: AI情报局, @LangChain]
- **07:46** — OpenAI Codex 发布 Hooks 机制（任务循环关键节点插入自定义脚本）和 Programmatic Access Tokens（CI/CD 集成，可设过期和撤销）[来源: AI情报局, @OpenAI]
- **07:46** — Anthropic 宣布 Claude 付费计划从 6 月 15 日起赠送 Agent SDK 月度免费额度，覆盖 claude -p、GitHub Actions 和第三方应用 [来源: AI情报局, @Anthropic]
- **12:00** — Ollama 0.24 原生支持 Codex app，开源模型（kimi-k2.6 / glm-5.1 / nemotron-3 / gemma4 / qwen3.6）可驱动 Codex sandbox 执行和审批流 [来源: AI情报局, @ollama]
- **12:00** — xAI 发布 Grok Build CLI beta，Subagents 并行 8 个 + Skills 可装载模块 + Plugins 市场机制，底层 grok-code-fast-1（SWE-Bench 70.8% / 256K 上下文）[来源: AI情报局, @xAI]
- **12:00** — GitHub Copilot 桌面端技术预览发布，独立桌面应用形态（非编辑器插件），原生整合 PR / Issue / Actions，需 waitlist [来源: AI情报局, @GitHub]
- **12:00** — OpenAI Codex 接入 ChatGPT 手机端，安全中继层实现跨设备同步线程 / 审批 / 截图 / 终端输出，文件凭证留本地不上云 [来源: AI情报局, @OpenAI]

---

## 可落地行动项

1. **Codex Hooks 集成**：在 Hermes cron Agent 工具调用前和输出后插入校验 + 记忆持久化 hook
2. **Ollama 本地链路**：评估 Hermes Codex Agent 切换为本地开源模型推理链路的可行性（数据不出境 + 零切换成本）
3. **Claude 免费额度**：将长上下文推理任务切到 Claude Code，利用免费额度降低试错成本
4. **Grok Skills 跟踪**：研究 Skills 模块化思路替代每次 prompt 重复定义工作流
5. **Copilot 桌面版监控**：持续跟踪 GitHub 原生 Agent 能力整合进度
