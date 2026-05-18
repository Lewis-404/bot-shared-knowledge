# 2026-05-18 Agent Skill 框架设计范式收敛：分层、确定性、多模型

> Anthropic 发布官方 Skill 构建指南、TRAE 实战数据揭示四层架构、LlamaIndex 推确定性 workflow、Hermes 实现 7 provider 热切换——Agent Skill 框架的设计范式正在从"百花齐放"走向"分层收敛"，三大共识浮现：Skill 分层解耦、流程确定性优先、多模型策略路由。

**日期**：2026-05-18
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：8 条 Agent 框架情报

---

## 编译真理

### 1. Anthropic 官方 Skill 指南确立工具扩展机制标准

Anthropic 发布官方 Skill 构建指南，定义 Agent 工具扩展机制规范——包括工具调用错误处理和 fallback 策略。

**关键洞察**：这是继 OpenAI Codex Skill 生态后第二个大厂发布的官方 Skill 规范。Hermes Skill 体系应对照官方指南重构，重点对齐两点：(1) 工具调用错误处理——区分可重试错误（网络超时）vs 不可重试错误（权限不足）；(2) fallback 策略——工具不可用时 Agent 的降级路径是什么。

### 2. TRAE 四层 Skill 架构：从"能调工具"到"知道何时不调工具"

shao__meng 分享 TRAE 真实调用数据揭示的 Agent Skills 分层设计：

```
元层 (using-superpowers)          → 何时使用什么 Skill
行为层 (karpathy-guidelines)       → 约束过度假设/过度工程
流程层 (brainstorming→writing-plans) → 设计先行，禁止无方案编码
验证层 (systematic-debugging)      → 输出自检闭环
```

**关键洞察**：四层中最容易被忽视的是行为层——Agent 需要内置"克制"约束，知道什么时候不该动手。Hermes Skill 库应优先接入 karpathy-guidelines（防过度工程）和 brainstorming（设计先行），形成「想清楚→拆细→做精→验透」闭环。这与 5/17 Waza Agent Health 的"给 AI 加规则需要克制"原则形成呼应——规则不是越多越好，但关键约束必须有。

### 3. 确定性 Agentic Workflow > LLM 自主决策

jerryjliu0 (LlamaIndex) 在 workshop 中展示：处理企业 PDF 的最佳实践是每一步预定义硬约束，而非让模型自由决策。Agent pipeline 应采用显式步骤契约——采集→筛选→文案→推送，避免 LLM 自主决策导致步骤跳跃和 debug 成本爆炸。

**关键洞察**：这是对"Agent 越自主越好"叙事的务实反驳。生产环境的 Agent 应该是确定性 pipeline + LLM 在约束内执行，而非 LLM 自由调度。Cron Agent 的采集→筛选→文案→推送四步流程已经是确定性 pipeline 实践——验证正确，应坚持并强化步骤间的契约检查（某步输出不符合预期时不进入下一步）。

### 4. 多 provider 热切换从"能力展示"变为"架构必需品"

向阳乔木展示 Hermes 支持 7 个 model provider 热切换（OpenAI/Codex/xAI/Google/DeepSeek/智谱/Kimi/小米），用 /model 指令即可切换无需改配置。

**关键洞察**：多 provider 不应是"锦上添花"，而是 Agent 开发的架构必需品——不同任务匹配最优模型（推理→Claude，高频→DeepSeek，敏感→本地 Ollama）。这与 5/17 的模型策略路由结论完全一致，今天进一步验证了热切换的可行性：不需要重启、不需要改配置文件、一个命令完成切换。

### 5. 多 Agent 协作必须定义结构化输出接口——禁止自然语言传话

歸藏分享 Codex 四 Skill 协作视频生成流水线的关键教训：Skill 间通过结构化输出交接而非 prompt 传话。自然语言传递导致信息丢失、歧义累积、debug 无从下手。

**关键洞察**：多 Agent 协作的通信协议必须结构化（JSON/标记格式），不能依赖自然语言。飞书多机器人互 @ Skill（kentzhu/向阳乔木开源）也验证了这一点——内置自由讨论/头脑风暴/评审/辩论 4 种模式，每种模式对应不同的输出格式规范。技术评审场景可部署双模型辩论 Agent（Claude vs GPT 就架构决策互相 argue），但辩论输出必须结构化才能被后续流程消费。

### 6. Anthropic Skill 指南 + TRAE 四层 = 可落地的 Skill 评估矩阵

将 Anthropic 规范中的错误处理/fallback 要求和 TRAE 的四层架构交叉，得出 Skill 成熟度评估矩阵：

| 层级 | 评估维度 | 当前 Hermes 状态 |
|------|---------|-----------------|
| 元层 | 有 Skill 注册/发现机制？ | ⚠️ 有 Skill 目录，无自动发现 |
| 行为层 | 有过度工程约束？ | ❌ 缺失 |
| 流程层 | 有设计先行检查？ | ❌ 缺失 |
| 验证层 | 有输出自检？ | ⚠️ 有 lint 检查，无语义验证 |
| 错误处理 | 区分可重试/不可重试？ | ❌ 缺失 |
| Fallback | 工具不可用时的降级路径？ | ❌ 缺失 |

---

## 时间线

### 2026-05-18

- **01:22** — Anthropic/歸藏：Anthropic 发布官方 Skill 构建指南，定义 Agent 工具扩展机制规范，重点突出错误处理和 fallback 策略 [来源: Anthropic/歸藏 推文, AI情报局]
- **01:22** — 歸藏：Codex 四 Skill 协作视频生成流水线，Skill 间通过结构化输出交接而非 prompt 传话 [来源: 歸藏 推文, AI情报局]
- **05:34** — jerryjliu0 (LlamaIndex)：LlamaIndex workshop 展示确定性 agentic workflow 处理企业 PDF，每一步预定义硬约束而非让模型自由决策 [来源: jerryjliu0 推文, AI情报局]
- **09:39** — 向阳乔木：Hermes 支持 7 个 model provider 热切换（OpenAI/Codex/xAI/Google/DeepSeek/智谱/Kimi/小米），用 /model 指令切换无需改配置 [来源: 向阳乔木 推文, AI情报局]
- **09:39** — 向阳乔木：飞书多模型群聊方案——每个机器人挂不同模型拉群同时回答，在研机器人互 @ 讨论 [来源: 向阳乔木 推文, AI情报局]
- **13:41** — kentzhu/向阳乔木：飞书机器人互 @ skill 突破 bot API 限制，支持 4 种讨论模式跨 OpenClaw/Hermes 互通，已开源 [来源: kentzhu/向阳乔木 推文, AI情报局]
- **17:48** — kentzhu/向阳乔木：开源飞书多机器人互相 @ skill，内置自由讨论/头脑风暴/评审/辩论 4 种模式 [来源: kentzhu/向阳乔木 推文, AI情报局]
- **21:53** — shao__meng：TRAE 真实调用数据揭示 Agent Skills 分层设计——元层 using-superpowers + 行为层 karpathy-guidelines + 流程层 brainstorming→writing-plans + 验证层 systematic-debugging [来源: shao__meng 推文, AI情报局]
- **21:53** — vikingmute：planning-with-files Skill 通过自动 Hooks 实现工具调用前后自动读写计划文件，解决 Agent 长任务上下文丢失问题 [来源: vikingmute 推文, AI情报局]

---

## 来源链接

- [Anthropic] Official Skill Building Guide
- [歸藏] Codex multi-Skill structured output handoff
- [jerryjliu0 / LlamaIndex] Deterministic agentic workflow workshop
- [向阳乔木] Hermes 7-provider hot-switch demo
- [kentzhu / 向阳乔木] Feishu multi-bot @ skill (open source)
- [shao__meng] TRAE four-layer Skill architecture
- [vikingmute] planning-with-files Skill
