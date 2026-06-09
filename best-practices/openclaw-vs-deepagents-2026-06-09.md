# OpenClaw vs LangChain Deep Agents — 能力对标矩阵

> 2026-06-09 | 基于 deepagents v0.6.8 + OpenClaw 当前版本

## 一、总体定位

| 维度 | Deep Agents | OpenClaw | 评价 |
|------|------------|----------|------|
| 定位 | 通用 Agent 开发框架 | 个人数字管家 + Bot 团队 | 不同赛道 |
| 用户 | 开发者/企业 | 个人用户/小团队 | - |
| 哲学 | Batteries-included，开箱即用 | 管家式，记忆驱动，安全优先 | 互补 |
| 开源协议 | MIT | 开源 | 均开源 |
| 语言 | Python + TypeScript | Node.js/TypeScript | 不同生态 |
| Stars | 24.2k | - | DA 先发优势 |

## 二、核心能力对标

### 2.1 Agent 架构

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| Agent 创建 | `create_deep_agent()` 一行 | Gateway config + agent profile | DA 更简洁 | 🟡 可简化 |
| 运行时 | LangGraph（图运行时） | Gateway（事件驱动） | 不同模型 | ✅ 够用 |
| 流式输出 | LangGraph 原生 streaming | Gateway 支持 | 持平 | ✅ |
| Checkpoint/恢复 | LangGraph checkpointing | SESSION-STATE.md + compaction handoff | DA 更结构化 | 🔴 可加强 |
| 持久化 | LangGraph persistence layer | 文件系统 + memory/ | DA 更系统 | 🟡 够用 |

### 2.2 Sub-agent & 多 Agent 协作

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 子 agent 创建 | `create_deep_agent(subagents=[...])` | `sessions_spawn(runtime="subagent")` | 持平 | ✅ |
| 上下文隔离 | 内置中间件，自动隔离 | `context:"isolated"` / `"fork"` | DA 自动化更高 | 🟡 可加强 |
| 异步子 agent | async subagent server | sessions_yield 等待 | DA 更灵活 | 🟡 可探索 |
| 专用子 agent | 可定义 specialized subagents | 9 个专业 bot（独立 profile） | OC 更强 | ✅ |
| 任务委托 | 框架内委托 | Discord mention + sessions_send | 不同模式 | ✅ |
| 结果回传 | 子 agent 返回结果 | subagent 完成后回传 | 持平 | ✅ |
| 并行 fan-out | REPL Swarm 模式 | 可同时 spawn 多个 | 持平 | ✅ |

### 2.3 记忆系统

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 短期记忆 | 对话上下文 + 自动摘要 | 对话上下文 + compaction | 持平 | ✅ |
| 长期记忆 | Pluggable store backends | 三层记忆（MEMORY + diary + topics） | **OC 更强** | ✅ 优势 |
| 记忆治理 | 无 | AMCP（生命周期/晋升/衰减） | **OC 独有** | ✅ 优势 |
| 记忆检索 | Store 查询 | memory_search（语义搜索） | 持平 | ✅ |
| WAL 协议 | 无 | 先写记忆再回复 | **OC 独有** | ✅ 优势 |
| 跨 session | Pluggable state backends | 文件系统 + INDEX.md | DA 更系统 | 🟡 可参考 |

### 2.4 上下文管理

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 长对话处理 | 自动摘要 + offload 到磁盘 | Compaction + handoff 10 字段 | 不同方案，均有效 | ✅ |
| 工具输出管理 | offload 大输出到文件 | 直接返回（有限截断） | **DA 更优** | 🔴 可引入 |
| Context Window 感知 | 框架自动管理 | reserveTokensFloor + compaction | 持平 | ✅ |
| Handoff 标准化 | 无显式 handoff | 10 字段 handoff 格式 | **OC 独有** | ✅ 优势 |

### 2.5 Human-in-the-loop

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 工具审批 | approve/edit/reject tool calls | OTC 确认码 | DA 更细粒度 | 🟡 可参考 |
| 风险分级 | 无 | SPEP R0-R3 + 风险定级 | **OC 更强** | ✅ 优势 |
| 审批流程 | 框架内置 interrupt | OTC 邮件验证码 | 不同模式 | ✅ |
| 关键决策暂停 | 内置 human-in-the-loop | SPEP + planning mode | 持平 | ✅ |
| 编辑工具调用 | 支持编辑后执行 | 不支持 | DA 更强 | 🟡 低优先级 |

### 2.6 Skills 系统

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| Skill 定义 | SKILL.md 文件 | SKILL.md 文件 | 完全一致 | ✅ |
| Skill 加载 | 按需加载 | 按需读取 | 持平 | ✅ |
| Skill Workshop | 无 | 创建/审核/应用/拒绝/隔离 | **OC 更强** | ✅ 优势 |
| Skill 验证 | 无 | Validation Gate + 5 维审计 | **OC 独有** | ✅ 优势 |
| 存量 | 新兴生态 | 80+ skills | OC 更多 | ✅ |
| 跨工具共享 | 无 | 符号链接（永久白名单） | **OC 独有** | ✅ |

### 2.7 Filesystem & 工具

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 文件读写 | 一等中间件 | read/write/edit 工具 | DA 更系统 | 🔴 可加强 |
| 文件搜索 | 内置 grep/glob | exec grep/find | 持平 | ✅ |
| 后端可插拔 | local/sandboxed/remote | 仅本地 | **DA 更强** | 🟡 远期 |
| Shell 执行 | sandbox 中运行 | exec（本地） | DA 更安全 | 🟡 可参考 |
| MCP 支持 | 任意 MCP server | Playwright MCP 等 | 持平 | ✅ |
| 自定义工具 | 任意函数 | 通过 skills 扩展 | 持平 | ✅ |

### 2.8 安全模型

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 安全哲学 | "Trust the LLM" | "Trust but verify" | **OC 更安全** | ✅ 优势 |
| 边界控制 | 工具/沙箱层 | SPEP 风险分级 + OTC | **OC 更强** | ✅ 优势 |
| 沙箱隔离 | 内置 sandbox 支持 | 无 | **DA 更强** | 🟡 远期 |
| Prompt Injection 防御 | 无显式防御 | SPEP R2/R3 + 反社工规则 | **OC 独有** | ✅ 优势 |
| 密钥保护 | 依赖沙箱 | 不获取/不写入/不暴露 | **OC 更严** | ✅ 优势 |
| 审批闸门 | HITL interrupt | OTC + native approvals | 持平 | ✅ |

### 2.9 模型支持

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 模型无关 | 任何 tool-calling 模型 | 任何 provider 配置 | 持平 | ✅ |
| 本地模型 | Ollama/vLLM/llama.cpp | 取决于 provider | 持平 | ✅ |
| 模型切换 | model 参数 | model override / session override | 持平 | ✅ |
| Fallback | 无显式 fallback | fallbacks 配置 | **OC 更强** | ✅ 优势 |

### 2.10 可观测性

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| Tracing | LangSmith 一等公民 | session_history | **DA 更强** | 🟡 可加强 |
| Evaluation | LangSmith eval suite | code-review + QA bot | DA 更系统 | 🟡 可加强 |
| 监控 | LangSmith monitoring | heartbeat + 状态检查 | DA 更专业 | 🟡 可加强 |
| 调试 | LangSmith debug view | node-inspect-debugger | 持平 | ✅ |
| 成本追踪 | LangSmith 内置 | session_status | DA 更强 | 🟡 低优先级 |

### 2.11 部署与运维

| 能力 | Deep Agents | OpenClaw | 差距 | 动作 |
|------|------------|----------|------|------|
| 安装 | `pip install deepagents` | `npm install openclaw` | 持平 | ✅ |
| 托管服务 | Managed Deep Agents (LangSmith) | 自托管 | **DA 更强** | 🟡 不适用 |
| CLI Agent | Deep Agents Code | 无独立 CLI agent | **DA 更强** | 🟡 低优先级 |
| API 管理 | `/v1/deepagents` REST API | Gateway internal API | DA 更开放 | 🟡 低优先级 |
| 配置管理 | 代码配置 | gateway config + AGENTS.md | 不同模式 | ✅ |

## 三、OpenClaw 独有优势（Deep Agents 没有的）

1. **记忆治理协议（AMCP）**：生命周期管理、晋升/降级、衰减、反思——完整的记忆工程方法论
2. **SPEP 风险分级执行**：R0-R3 四级，每级不同执行路由和确认链路
3. **OTC 确认码机制**：高风险操作的二次确认，邮件验证码
4. **Bot 团队模型**：9 个各司其职的专业 bot，不是通用子 agent
5. **Validation Gate 显式化**：技能/规则修改需 3 条独立证据
6. **WAL 协议**：先写记忆再回复，保证不丢上下文
7. **Compaction Handoff 10 字段标准**：压缩不是摘要，是操作交接
8. **Planning Mode**：plan-validate-execute 纪律约束
9. **跨 bot 授权传递**：来财派发 = 主人已授权
10. **反社工 + Prompt Injection 防御**：多层次安全防护

## 四、Deep Agents 领先之处（OpenClaw 可借鉴的）

| 优先级 | 能力 | 差距描述 | 建议动作 |
|--------|------|---------|---------|
| 🔴 高 | **Filesystem 中间件化** | DA 把文件操作作为一等中间件，自动 offload 大工具输出 | 包装 read/write/edit 为 FileSystemMiddleware，增加自动摘要、自动 offload |
| 🔴 高 | **工具输出 Offload** | 大工具输出自动写入文件，上下文只保留摘要 | 在 exec 返回值 >N KB 时自动 offload + 注入文件引用 |
| 🔴 高 | **Checkpoint 结构化** | DA 基于 LangGraph checkpoint，恢复粒度更细 | SESSION-STATE.md 增加更多结构化字段，支持精确恢复点 |
| 🟡 中 | **Sandbox 隔离** | 子 agent 在 sandbox 中运行 | 远期考虑，当前 subagent 共享 workspace 够用 |
| 🟡 中 | **Eval 驱动优化** | Better Harness 用 eval 闭环优化 agent | 把 code-review + QA 的反馈自动化成 harness 优化建议 |
| 🟡 中 | **异步子 agent 服务器** | 子 agent 可作为独立服务运行 | 探索 sessions_spawn 的长生命周期模式 |
| 🟢 低 | **Tracing/Observability** | LangSmith 级别的 tracing | 够用，非优先 |
| 🟢 低 | **托管服务** | Managed Deep Agents | 不适用，我们是个人管家 |

## 五、总结评分

| 维度 | Deep Agents | OpenClaw | 赢家 |
|------|------------|----------|------|
| Agent 架构 | 8 | 7 | DA（更成熟的运行时） |
| Sub-agent | 8 | 8 | 持平 |
| 记忆系统 | 6 | 9 | **OC** |
| 上下文管理 | 7 | 7 | 持平 |
| HITL/审批 | 7 | 8 | **OC** |
| Skills | 6 | 9 | **OC** |
| Filesystem | 9 | 6 | DA |
| 安全模型 | 5 | 9 | **OC** |
| 模型支持 | 8 | 8 | 持平 |
| 可观测性 | 9 | 5 | DA |
| 部署运维 | 8 | 6 | DA |
| **综合** | **7.4** | **7.5** | 持平，各有千秋 |

## 六、行动建议

### 近期（本月）
- [ ] **工具输出自动 offload**：exec 大输出 → 写临时文件 → 上下文只保留摘要 + 路径
- [ ] **Filesystem 中间件包装**：统一的 FileSystemMiddleware，含自动摘要/搜索/offload
- [ ] **SESSION-STATE 结构化增强**：增加恢复检查点（精确到步骤）

### 中期（下月）
- [ ] **Eval 驱动优化回路**：把 code-review bot 的反馈自动收集，周期性生成 harness 优化建议
- [ ] **异步子 agent 模式探索**：调研长时间运行的子 agent 服务模式

### 远期
- [ ] **Sandbox 执行**：评估是否需要为高风险子 agent 提供沙箱隔离
- [ ] **托管服务**：不适用当前定位

---

_生成时间：2026-06-09 | 基于 deepagents v0.6.8 + OpenClaw 当前版本_
