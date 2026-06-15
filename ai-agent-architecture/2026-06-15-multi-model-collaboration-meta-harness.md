# 多模型 Agent 协作与 Meta-harness 架构 — 2026-06-15 行业前沿

- **来源**：
  - https://x.com/geekbb/status/2066379169634947435
  - https://x.com/shao__meng/status/2066326398252499391
  - https://x.com/FinanceYF5/status/2066348364426600478
- **日期**：2026-06-15
- **标签**：#AI-Agent #Multi-Model #Meta-Harness #Agent-Architecture #OpenClaw
- **摘要**：三篇行业前沿文章分别探讨多模型 Agent 分工协作工作流、Databricks Omnigent meta-harness 架构，以及 Claude Fable 5 被叫停事件，共同指向 Agent 系统的下一阶段——从单模型单 harness 走向多模型组合、统一治理、协作共享。

---

## 一、多模型分工工作流（Geek）

### 核心实践

> Claude Fable 5 负责规划和评审，GPT-5.5 Codex 负责干活和研究。
> 仓库即记忆，门禁在代码出来前就写死。

### 关键理念拆解

**1. 角色分工，而非模型替代**
- Claude Fable 5 → 规划者、评审者（架构师角色）
- GPT-5.5 Codex → 执行者、研究者（工程师角色）
- 不同模型有不同的强项，不是用更强的模型替换，而是让各自做最擅长的事

**2. "仓库即记忆"（Repo as Memory）**
- 用 git 仓库本身作为 Agent 的持久化记忆载体
- 解决单模型编码时"上下文越跑越蠢"的问题——每次新 session 从仓库状态恢复上下文
- 本质是用版本控制的确定性对抗 LLM 上下文的不确定性

**3. "门禁在代码出来前就写死"（Gate Before Code）**
- 验收标准在代码生成前就确定，不被生成结果"带着走"
- 对抗"自己给自己打分"的自评偏差
- 对抗"验收标准跟着结果跑"的滑坡效应

**4. 单模型编码三大老毛病**
| 毛病 | 表现 | 解法 |
|------|------|------|
| 上下文越跑越蠢 | 长 session 中模型逐渐偏离目标 | 仓库即记忆，每次从确定状态出发 |
| 自己给自己打分 | 生成的代码自己评估，缺乏客观性 | 评审者与执行者分离 |
| 验收标准跟着结果跑 | 代码生成后重新定义"什么是好的" | 门禁前置，标准写死在代码出来前 |

---

## 二、Databricks Omnigent — Meta-harness 架构（meng shao）

### 核心判断

> Agent 能力的瓶颈，正在从「模型/harness 本身」上移到「如何组合、治理、协作多个 Agent」。

### Omnigent 是什么

在现有 Agent（Claude Code、Codex、Pi、自研 Agent）之上，提供**统一接口、策略层和协作层**。

**关键设计洞察**：无论底层 harness 如何调用 LLM，对用户界面本质相同：
```
messages + files in → text streams + tool calls out
```
Omnigent 据此抽象出通用 API，覆盖 CLI coding agent 和 SDK。

### 三大能力支柱

#### 1. Composition（组合）
- 一行配置切换 Claude Code ↔ Codex ↔ Pi ↔ 自研 Agent
- YAML 定义 custom agent，可跨 harness 移植
- 同一 Agent 内可组合不同 harness 的 subagent
- 内置 Polly（coding orchestrator）、Debby（model debate）
- **价值**：把"选哪个 harness"从架构决策降成配置决策

#### 2. Control（控制）
区别于 prompt 级 guardrail，在 meta 层做**有状态、上下文感知的策略**：
- **成本策略**：按 session 追踪 LLM 花费，如每 $100 暂停并请求继续
- **上下文安全策略**：npm 安装新包后 git push 需人工批准；Agent 只能写自己创建的 doc
- **OS 沙箱**：灵活限制文件系统/网络；凭证对 Agent 不可见，由 egress proxy 在批准时注入
- **价值**：策略与 harness 解耦，换 Agent 不换治理逻辑

#### 3. Collaboration（协作）
- 通过 URL 共享 live agent session
- 多人同时查看 workspace 文件、评论、发送命令
- 同一 Agent 可从 terminal、Web、macOS App、mobile、REST API 访问
- 可在本机或 Modal/Daytona 等托管沙箱中运行
- **价值**：Agent session 从个人终端工具变成可共享的协作 surface

### 类比：Kubernetes / Terraform

工程师不再管单个进程/服务器，而是管整个 fleet。Agent 领域同理——模型和 harness 会持续变化，你工作的抽象层不应随之反复重建。Meta-harness 让 session、policy、skills 与具体 harness 解耦，形成可迁移的工作层。

---

## 三、Claude Fable 5 被叫停事件（AI Will）

- Claude Fable 5 被美国政府以国家安全为由叫停
- 其 3 天内产出的成果已极为丰富（10+ 案例合集）
- **隐含信号**：前沿模型面临政策风险，多模型策略是风险对冲的必要手段

---

## 四、对 OpenClaw / 来财体系的意义分析

### 4.1 与当前架构的映射

| Omnigent 概念 | OpenClaw 对应 | 成熟度 |
|--------------|---------------|--------|
| Composition（组合切换） | 多 bot 架构（10 bot 各司其职） | 🟡 已有多 bot，但切换不灵活 |
| Control（策略控制） | SPEP 统一执行协议 + OTC | 🟢 已有完整的策略层 |
| Collaboration（协作） | Discord 群聊 + sessions_spawn | 🟡 有协作但缺实时共享 |
| Meta-harness 统一接口 | 来财作为中央协调者 | 🟡 人工协调为主，非程序化 |

### 4.2 已验证的设计选择

1. **多模型策略正确**：Claude Fable 5 被叫停验证了不依赖单一模型的必要性
2. **角色分工方向正确**：来财（协调）+ 9 专业 bot（执行）的模式与 Geek 的实践一致
3. **SPEP 统一策略层**：与 Omnigent 的 Control 理念一致——策略与具体 harness 解耦
4. **记忆系统价值**：我们的 MEMORY.md + memory/ 虽然不如"仓库即记忆"彻底，但方向一致

### 4.3 可改进的方向

1. **"门禁前置"强化**：当前 SPEP 前置了风险判断，但验收标准还不是完全前置的。可以考虑在执行前显式声明"预期结果是什么样的"
2. **记忆系统增强**：考虑将关键决策和偏好纳入 git 版本控制（已经在做，但可以更系统化）
3. **Composition 灵活性**：当前 bot 之间的切换依赖 Discord mention，可以考虑配置化的热切换
4. **协作 surface 扩展**：Omnigent 的 session URL 共享给我们启发——未来可以让 bot session 可见、可介入

### 4.4 风险提醒

- Databricks 5000+ 工程师的实践规模远超我们，Omnigent 的复杂度可能不适合直接照搬
- "仓库即记忆"对个人项目有效，但需要评估 overhead
- Meta-harness 本身也是新的锁定点——如果 Omnigent 成为唯一抽象层，换 Omnigent 本身也是成本

---

## 五、行动建议

| 优先级 | 行动 | 理由 |
|--------|------|------|
| P1 | 保持多模型策略，不依赖单一模型 | Fable 5 被叫停是最新警示 |
| P2 | 在 SPEP 中加入"验收标准前置"环节 | 直接借鉴"门禁前置"理念 |
| P2 | 关注 Omnigent 开源进度 | 如果开源可评估集成 |
| P3 | 探索 bot session 可观测性 | 借鉴 Collaboration 理念 |
| P3 | 评估 git 作为记忆载体的可行性 | 长期方向，短期不紧急 |

---

_由来财学习三篇行业前沿文章后整理，2026-06-15_
