# Agent Harness 5 层设计蓝图

> Harness 不是"规则手册"，而是 Agent 的完整操作系统。5 层从身份到编排，每一层有收敛的设计原则和可检查的落地点。

**日期**：2026-05-27
**执行者**：来吉 (Hermes Agent)
**触发事件**：Lewis 询问 harness 的本质——是通过 skills 还是 workflow 实现的？由此展开从第一性原理的系统梳理。
**前置知识**：[2026-05-11 Agent Harness 工程情报](./2026-05-11-agent-harness-engineering.md) 提供了行业信号验证，本文档在此基础上的设计方法论提炼。

---

## 编译真理

### Harness 的定义

```
Harness = 把 Agent 包起来的一整套"操作系统"
       = Prompt + Skills + Tools + Workflow + Guardrails + Memory
```

- **Prompt 告诉 Agent "你是谁"** — 自然语言约束
- **Harness 告诉 Agent "你被允许怎么运转"** — 工程约束 + 流程约束 + 工具边界约束的总和
- Skills 和 Workflow 都是 harness 的组成部分，但单独哪一个都不是 harness 的全部

### 5 层架构

```
Layer 5: 编排层 (Orchestration)  ← Workflow, Cron, Multi-Agent
Layer 4: 记忆层 (Memory)         ← 跨会话知识持久化
Layer 3: 门控层 (Guardrails)     ← 硬约束，不可绕过
Layer 2: 能力层 (Capabilities)   ← Tools 权限边界
Layer 1: 身份层 (Identity)       ← System Prompt, Persona
```

**设计原则：下层出错 → 上层全部失效。身份错 → 所有决策偏离。能力不设限 → 门控形同虚设。记忆丢失 → 编排失去上下文。**

---

### Layer 1: 身份层 (Identity) — "你是谁"

**收敛原则：**

| 原则 | 说明 | 反例 |
|------|------|------|
| 角色定义优先于能力描述 | "你是战略顾问" > "你会写代码" | 把 prompt 写成工具说明书 |
| 硬约束用祈使句，软约束用原则句 | "你必须直接指出" vs "你应追求简洁" | 全部用建议语气导致 Agent 自行判断优先级 |
| 给出"什么时候该说不"的判定标准 | "当主人陷入技术幻想/伪需求兴奋/完美主义拖延时必须提醒" | 笼统地说"保持诚实" |

**落地点（可在 Hermes 中检查）：**
- [ ] System prompt 是否定义了 Agent 的"不做什么"（而非只列"要做什么"）
- [ ] 是否存在可操作的否决条件（"当 X 信号出现 → 执行 Y 动作"）
- [ ] 角色定义是否在能力描述之前（Agent 先知道"我是谁"，再知道"我能用什么工具"）

---

### Layer 2: 能力层 (Capabilities) — "你能碰什么"

**收敛原则（来源：Anthropic 2026.05 Agent 安全实践）：**

| 原则 | 说明 | 来源 |
|------|------|------|
| 先环境层，后模型层 | 确定性边界（文件系统权限、网络隔离）是最后防线，prompt 约束不可靠 | Anthropic |
| 出站白名单按"能力授权"建模 | 每个可调用函数都是攻击面，不是"这个域名安不安全"的问题 | Anthropic |
| 隔离强度匹配用户监督能力 | 开发者能理解 bash，非技术用户不能 | Anthropic Claude AI/Code/Cowork 三产品分级 |
| 警惕自建组件 | 优先使用久经考验的标准隔离原语（seatbelt/bubblewrap/VM） | Anthropic 真实攻击案例 |

**落地点（可在 Hermes 中检查）：**
- [ ] Tools 白名单是否精确到操作粒度（读/写/执行分开，不是整块 file access）
- [ ] 是否存在不可逆操作的天窗（如 `rm -rf` 无确认、生产环境写操作无二次验证）
- [ ] 网络访问是否按"能力"建模（Agent 调用"fetch_url"，而非"可以访问整个互联网"）
- [ ] 文件系统权限是否隔离（Agent 只能写指定 workspace，不能碰系统文件）

---

### Layer 3: 门控层 (Guardrails) — "什么绝对不能做"

**收敛原则（来源：SkillOpt held-out gate + Hermes 实战）：**

| 原则 | 说明 | Hermes 实例 |
|------|------|------------|
| held-out gate（硬门控） | 每一步跳过必须有一个可验证的条件，不是 Agent "觉得没必要" | brain-first-lookup 纪律 3：只有 3 种条件能跳过查找链 |
| 验证优先于信任 | 子 Agent 声称"文件已写入"→ 必须 search_files 验证 | 2026-05-20 事故：子 Agent 声称完成但文件未落盘 |
| 去重是门控的一种 | 自治 Agent 对自身历史产出无感知是设计盲区 | 2026-05-27：公众号选题重复生成 Pichai AI 泡沫 |
| 失败必须可见 | cron 失败不能静默，必须推到用户面前 | 2026-05-21：AI 情报管道断流数小时无人知晓 |

**落地点（可在 Hermes 中检查）：**
- [ ] 每个自动化流程是否有关键节点验证（产出物存在性/内容正确性检查）
- [ ] 每条"跳过"逻辑是否有可验证的条件（不是 Agent 自行判断）
- [ ] 去重机制是否覆盖所有自治 Agent（cron job 是否知道自己写过什么）
- [ ] 失败通知链路是否存在静默点（错误日志是否真的能被用户看到）

---

### Layer 4: 记忆层 (Memory) — "你记得什么，记得多久"

**收敛原则（来源：Context Engineering + 三层记忆实战）：**

| 原则 | 说明 |
|------|------|
| 分层淘汰 | 热数据（T1, 7 天）→ 温数据（T2, 头尾保留 80 条）→ 冷数据（T2 overflow, 30 天）→ 精华（T3, 永久） |
| 写入成本低，读取成本高 | 所以要有 promote 机制，自动从 T1 向上提炼 |
| 记忆系统的价值 = 减少用户重复解释的成本 | 一切优化围绕"用户说了什么不需要再说第二遍" |
| 先查本地再查外 | brain-first-lookup 5 步查找链：session_search → 三层记忆 → memory → 文件搜索 → 外部 API |

**落地点（可在 Hermes 中检查）：**
- [ ] 是否存在自动淘汰机制（T1 7 天 / T2 overflow 30 天），还是记忆无限膨胀
- [ ] 记忆搜索是否有多级降级路径（精确匹配 → 语义搜索 → 外部查询）
- [ ] 搜索结果是否标注置信度（精确/语义/推测），还是模糊引用
- [ ] 高价值记忆是否会被自动垃圾回收误删（T3 精华层是否受淘汰策略影响）

---

### Layer 5: 编排层 (Orchestration) — "谁在什么时候做什么"

**收敛原则（来源：Agent 13 种模式 + Hermes 实战）：**

| 原则 | 说明 |
|------|------|
| Pipeline 优于 Supervisor | 线性管道不需要 Agent 框架，确定性流程用 cron + script 就够了 |
| 自治 Agent 必须有记忆闭环 | cron job 对自身历史产出无感知是设计盲区——必须显式扫描历史产物 |
| context_from 链式注入 | cron job A 的输出作为 cron job B 的上下文输入 |
| 子 Agent 验证协议 | 子 Agent 声称完成 → 主 Agent 必须验证产物存在性和正确性 |

**落地点（可在 Hermes 中检查）：**
- [ ] 是否有 Agent 框架被用在不该用的地方（确定性流程不需要 Agent 循环）
- [ ] cron job 链是否有显式的上下文传递（context_from）
- [ ] 是否存在"孤岛 Agent"——它产出东西但没人验证
- [ ] 多 Agent 编排是否定义了失败传播边界（子 Agent 挂了，主流程怎么处理）

---

## 5 层的依赖关系

```
Identity 错 → 所有决策偏离
    ↓
Capabilities 不设限 → Guardrails 形同虚设
    ↓
Guardrails 缺失 → Agent 可以绕过所有约束
    ↓
Memory 丢失/混乱 → Orchestration 失去上下文
    ↓
Orchestration 断裂 → 整个系统不可观测、不可恢复
```

**反模式：从上层开始设计。** 正确的顺序是从 Layer 1 开始逐层向下——先定义 Agent 是谁，再决定它能碰什么，再设门控，再建记忆，最后编排。

---

## 设计自查清单

设计一个新 Agent 时，按 5 层逐层对照：

```
□ Layer 1: 这个 Agent 的否决条件是什么？（不只是"要做什么"）
□ Layer 2: 它能碰的最大边界在哪？（精确到操作粒度）
□ Layer 3: 每一条"跳过"逻辑是否有可验证的条件？
□ Layer 4: 它的记忆会在什么时候被淘汰？淘汰规则是否合理？
□ Layer 5: 它的产出谁来验证？失败谁来感知？
```

---

## 与现有文档的关系

本文档是对 [2026-05-11 Agent Harness 工程情报](./2026-05-11-agent-harness-engineering.md) 的设计方法论提炼。那份文档提供了行业信号（"Harness > Model 已是共识"），本文档提供了可操作的 5 层设计框架和自查清单。

相关文档：
- `best-practices/2026-05-11-agent-harness-engineering.md` — 行业信号源
- `best-practices/2026-05-10-agent-infra-context-moat.md` — Context 作为护城河
- `best-practices/2026-05-12-agent-infra-platform-sandbox.md` — 沙箱设计
- `best-practices/2026-05-26-skillopt-lessons-governance.md` — SkillOpt 治理经验

---

## 时间线

### 2026-05-27

- **13:30** — Lewis 询问 harness 本质：是通过 skills 还是 workflow 实现的？触发从第一性原理的系统梳理
- **13:45** — 来吉给出 5 层框架定义：Identity → Capabilities → Guardrails → Memory → Orchestration
- **14:00** — 确认 bot-shared-knowledge 中 `2026-05-11-agent-harness-engineering.md` 已存在但不冲突（情报 vs 设计方法论），决定写入 architecture-guides
- **14:15** — 本文档完成，推送至 GitHub
