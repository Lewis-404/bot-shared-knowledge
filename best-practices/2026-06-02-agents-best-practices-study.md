# 2026-06-02 开源项目学习：DenisSergeevitch/agents-best-practices

> 一份「Agent 运行时 Harness」的完整架构设计手册，以可移植 Agent Skill 形式打包。覆盖 15 个主题的深度参考文档，1.5K+ stars，MIT 协议。

**日期**：2026-06-02
**执行者**：来吉 (Hermes Agent)
**来源**：https://github.com/DenisSergeevitch/agents-best-practices
**触发事件**：Lewis 要求学习该项目并整理总结

---

## 编译真理

### 1. 「模型提议，Harness 执行」是 Agent 架构的第一性原理

这个项目最核心的一句话：

> **"The model proposes actions; the harness validates, authorizes, executes, records, and returns observations."**

它把 Agent 系统严格拆成两层，模型和 Harness 各司其职：

| 模型的职责 | Harness 的职责 |
|---|---|
| 解释用户意图 | 组装指令和上下文 |
| 选择下一步推理/行动 | 决定哪些工具可见 |
| 用结构化调用请求工具 | 验证工具参数 |
| 合成观察结果 | 强制执行权限和审批 |
| 输出最终答案或计划 | 执行工具、记录状态、追踪日志 |
| | 执行预算和停止条件 |

**模型永远不直接执行动作。** 模型只能「提议」，Harness 才是「执行者」。

### 2. Agent 循环的 7 个不变量——每条都必须在代码中强制执行

```text
while not done:
  构建上下文 → 调用模型 → 模型返回最终答案或工具调用
  → 验证每个工具调用 → 检查权限和审批策略
  → 执行或拒绝 → 追加结构化结果
  → 必要时压缩/检索上下文 → 在完成或预算耗尽时停止
```

7 个不变量：

1. 每个工具调用必须收到恰好一个结果
2. 工具参数在解析和验证后才能执行
3. 每次副作用前必须做权限决策
4. 工具结果必须是有界的、结构化的、可追踪的
5. 循环必须有硬性步骤/时间/token/成本预算
6. 最终答案基于观察结果，而非假设工具成功
7. 错误、拒绝、取消、超时都变成结构化观察结果

### 3. 工具设计：窄而专 + draft/commit 分离

不要暴露 `execute_anything`、`write_database`、`send_message` 等宽泛工具。每个工具应该是领域语义化的窄工具。

**风险分类体系**（16 类）：
```
read_only / search_only / compute_only / draft_only
write_local / write_internal / write_external
financial / communication / identity_access
security_sensitive / process_execution
network_open_world / destructive / privileged_admin
```

**最核心的设计模式：draft/commit 分离**
```
draft_email → send_email
prepare_refund → issue_refund
propose_record_update → apply_record_update
prepare_contract_change → submit_contract_change
```

draft 工具通常可以自动执行。commit 工具需要审批，除非是低风险且明确允许的。

### 4. Harness 成熟度 6 级——默认从最低级别开始

```
Level 0: 纯回答助手（无工具执行）
Level 1: 检索 Agent（可搜索读取，无副作用）
Level 2: 起草 Agent（可提议动作，不能提交）  ← 大多数 MVP 从这里开始
Level 3: 审批门控行动者（执行前需审批）       ← 大多数商业 Agent 的默认级别
Level 4: 策略约束自主行动者（低风险动作可自主）
Level 5: 长期目标工作者（跨会话持续执行）
```

**原则**：只在低级别确实不够用时才升级。

### 5. 上下文不是数据库——持久状态必须存在 prompt 之外

Prompt 不应存储：
- 活动计划、活动目标、todo 列表
- 审批记录、工具追踪、工作流状态
- 加载的指令作用域、技能调用记录
- 压缩摘要、评估结果

压缩不是对话摘要，而是**运营状态移交**。压缩后必须保留：当前目标、用户约束、活动的计划/审批/目标状态、关键事实、已完成动作、待办任务、下一步建议。

### 6. 实施路径有标准顺序——不要跳步

```
手动循环 → 工具 → 权限 → 结构化观察 → 预算
→ 追踪 → 规划 → 上下文/记忆 → 压缩
→ Skills/连接器 → 目标循环 → 子Agent
```

在单 Agent 循环通过评估之前，不要引入多 Agent 系统。

### 7. 反复失败要变成 Harness 功能，而不是反复改 prompt

这是一个核心工程纪律：
```
Agent 失败或变慢
  → 识别缺失的能力、上下文、验证器或权限规则
  → 把修复编码为文档、工具、策略、schema 或 eval
  → 重新运行并测量
  → 把改进保留为 Harness 的一部分
```

不应该做的事：反复改 prompt 里的文字，期望下一次模型会更听话。

### 8. MVP 蓝图生成器——设计 Agent 时的 15 段标准化模板

当需要设计一个新 Agent 时，产出一个 15 段蓝图：

```
1. 目标　　　　　　　　2. MVP 范围 + 假设
3. 自主权/风险级别　　　4. 核心循环
5. 上下文/指令架构　　　6. 工具注册表
7. 规划行为　　　　　　8. 目标循环
9. 上下文/记忆/压缩　　10. Skills/连接器
11. Prompt 缓存/成本　 12. 安全/审批
13. 可观测/评估　　　　14. 最小实施路径
15. 上线检查清单
```

这个模板不仅适用于编码 Agent，也适用于研究、销售、金融、客服、法律、医疗、教育、采购等任何领域的 Agent。

---

## 时间线

### 2026-06-02
- **来吉** — 通过 GitHub API + curl 通读项目全部 17 个文件（README、SKILL.md、15 篇 references）
- **来吉** — 与 bot-shared-knowledge 已有内容去重检查：无真实重叠（已有 `agent-harness-5-layer-blueprint.md` 是你的架构总览，`agent-harness-engineering.md` 是行业情报，本文档是具体的开源项目源码级学习笔记）
- **来吉** — 写出编译真理 8 条 + 核心参考文件索引

---

## 核心参考文件索引

| 文件 | 用途 | 适用场景 |
|------|------|---------|
| mvp-agent-blueprint.md | 15 段标准化 Agent 蓝图模板 | 设计新 Agent 时直接套用 |
| agentic-loop.md | 循环不变量 + 重试 + 预算 | 实现 Agent 主循环时参考 |
| tools-and-permissions.md | 工具契约 + 16 类风险 + draft/commit | 设计工具和权限矩阵 |
| architecture.md | 完整组件模型 + 成熟度分级 | 全局架构设计 |
| context-memory-compaction.md | 上下文分层 + 压缩算法 + 恢复 | 长会话记忆管理 |
| prompt-caching-and-cost.md | 缓存策略 + 成本控制 | 生产环境成本优化 |
| planning-and-goals.md | 规划模式 + 目标循环 | 多步骤任务设计 |
| workflow-orchestration.md | 工作流编排 + 验证 + 集成 | 大规模可分解任务 |
| skills-and-connectors.md | Skills/MCP/外部连接器治理 | 扩展 Agent 能力边界 |
| security-evals-observability.md | 安全威胁模型 + 评估策略 + 上线门禁 | 生产上线前 |
| checklists.md | 全维度检查清单 | 实施审计和自查 |
| agent-legibility-feedback-loops.md | Agent 可读环境 + 反馈循环 + 熵管理 | 长期维护 |

## 与 Hermes 当前架构的映射

| agents-best-practices 概念 | Hermes 对应 |
|---|---|
| Agent 循环 | terminal + 工具调用 + 响应循环 |
| 工具注册表 | Hermes 工具系统 (terminal/read_file/write_file 等) |
| 权限矩阵 | Hermes 有工具权限但缺少细粒度风险分级 |
| 上下文构建 | Memory 注入 + system prompt |
| 记忆/压缩 | T1/T2/T3 三层记忆系统 |
| Skills | Hermes Skills 系统 |
| 工作流编排 | delegate_task + cronjob |
| 安全/评估 | ⚠️ 评估体系待建设 |
| Prompt 缓存 | ⚠️ 未显式设计 |
| 预算控制 | ⚠️ 缺少硬性步骤/时间/token 预算 |
| draft/commit 分离 | ⚠️ send_message 等工具无 draft 模式 |

## 关键资源

- 项目地址：https://github.com/DenisSergeevitch/agents-best-practices
- Agent Skills 规范：https://agentskills.io/specification
- 上游参考：OpenAI Agents SDK、Anthropic building effective agents、MCP 规范
