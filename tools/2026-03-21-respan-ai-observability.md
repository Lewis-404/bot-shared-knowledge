# Respan — 主动式 AI 可观测性平台
- 来源: https://siliconangle.com/2026/03/18/respan-raises-5m-bring-proactive-observability-ai-agents/
- 日期: 2026-03-21
- 标签: observability, ai-governance, llm-monitoring, agent-monitoring, funding, YC
- 摘要: YC 孵化的主动式 AI 可观测性平台，500 万美元融资（Gradient 领投），核心卖点是"主动发现问题"而非被动告警，已服务 100+ 团队。

## 要点

### 是什么
面向 AI Agent 应用的**主动式**可观测性平台。区别于传统 APM 的被动监控，Respan 强调在问题发生前主动识别风险。闭源 SaaS 产品，非开源。

### 创始团队
- CEO Aman Dhesi（前 Datadog 工程师）+ CTO Aman Mangal（前 Datadog 工程师）
- 两人在 Datadog 期间负责过大规模可观测性系统，将传统 APM 经验迁移到 AI 领域

### 融资
- 500 万美元，Gradient 领投
- 跟投：Y Combinator、Hat-Trick Capital、Xiaoxiao Fund、Antigravity Capital、Alpen Capital
- 多位 AI 领域天使投资人参与

### 核心产品能力

1. **主动式观测（核心差异化）**
   - 不只是被动收集指标和告警
   - 主动建议下一步评估、推荐变更、采样生产流量
   - 在 Agent 上线前（dev/staging）就能观测和评估行为

2. **三阶段覆盖**
   - Development → Staging → Production 全链路观测
   - 团队可以在 Agent 进入生产环境前发现问题

3. **LLM 温度调优建议**
   - 平台会建议将 LLM 温度设为 0（确定性输出）
   - 低温度迫使模型选择最安全的选项，更严格地遵循指令

4. **告警集成**
   - 通过 Slack、Email、短信触发告警
   - 支持自动采样生产流量进行评估

### 行业定位
- 传统 APM（Datadog、New Relic）不够用：LLM 调用的非确定性、token 成本、prompt 质量等是新维度
- Respan 定位为"AI 原生的可观测性"，不是在传统 APM 上加 AI 功能
- 已服务 100+ 创业公司和企业团队

## 对我们的价值

### Phase 3（Observability v1）直接参考

Respan 的设计思路与我们里程碑 Phase 3 高度相关：

| 维度 | Respan 思路 | 我们的现状 | 差距 |
|------|------------|-----------|------|
| 主动观测 | dev/staging/prod 三阶段 | 仅 prod 日志 | 缺 dev/staging 观测 |
| 调用追踪 | Agent 行为全链路 | 日志+日记 | 无结构化 trace |
| 成本归因 | 按任务/Agent | session_status 手动查 | 无自动归因 |
| 质量监控 | 温度调优/行为评估 | 无 | 完全缺失 |
| 告警 | Slack/Email/SMS | 无自动告警 | 完全缺失 |
| 回放调试 | 生产流量采样 | sessions_history | 基本可用 |

### 最值得借鉴的三个点

1. **"主动"而非"被动"**：不只是出了问题看日志，而是持续评估 Agent 行为是否在预期范围内。我们可以在 heartbeat 中加入 Agent 行为基线检测。

2. **三阶段观测**：我们目前只在"生产"环境观测。可以考虑在来宝/来喜编码时就采集行为数据，上线前做评估。

3. **前 Datadog 团队的经验迁移**：传统 APM 的 trace/metric/log 三支柱在 AI 场景下需要扩展为 trace/metric/log + token cost/prompt quality/agent behavior。

### 局限
- 闭源 SaaS，我们不会直接使用（数据不出本地）
- 具体技术实现细节未公开
- 作为设计参考框架使用，具体实现需结合 OpenClaw 架构自研
