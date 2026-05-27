# Best Practices 索引

这里收录经过实战验证的最佳实践和实验总结。

---

## 📚 内容列表

### 2026-03-18: Claude Code Skills 8条沉淀原则
**文件**: [2026-03-18-claude-code-skills-8-principles.md](2026-03-18-claude-code-skills-8-principles.md)

**核心结论**:
- ✅ Skills 是“可组合能力包”，不是纯提示词
- ✅ description 要写触发条件而非功能简介
- ✅ gotchas / 渐进披露 / 验证 / 度量是高 ROI 关键点

**适用场景**:
- 多 bot 协作的技能库建设
- 团队化 AI 编程工作流
- 提升 Skill 命中率与可维护性

---

### 2026-03-15: OpenClaw Telegram 连接问题排查报告
**文件**: [2026-03-15-openclaw-telegram-launchagent-network-isolation.md](2026-03-15-openclaw-telegram-launchagent-network-isolation.md)

**核心问题**:
- macOS LaunchAgent 网络隔离导致 OpenClaw 无法通过 TUN 模式访问 Telegram API

**关键发现**:
- ✅ TUN 模式本身正常（普通进程可用）
- ❌ LaunchAgent 进程运行在隔离沙箱中，不继承用户网络配置
- ✅ 显式配置代理可绕过隔离问题

**解决方案**:
- 管理脚本 + 环境变量（`HTTP_PROXY`/`HTTPS_PROXY`）
- 配置文件显式指定代理（`channels.telegram.proxy`）

**适用场景**:
- macOS 上运行 OpenClaw Gateway
- 使用 Clash/V2Ray 等代理工具的 TUN 模式
- 需要访问被墙服务（Telegram、OpenAI 等）

---

### 2026-03-14: Agentic Checklist 10天实验总结
**文件**: [2026-03-14-agentic-checklist-experiment.md](2026-03-14-agentic-checklist-experiment.md)

**核心结论**:
- ✅ 记忆写入纪律有效（明确触发条件 + `printf >>` 追加）
- ⚠️ 定期任务需要"变化检测"机制（否则陷入形式主义）
- ❌ "会话短而专"不适用于私人助理场景

**适用场景**:
- 单任务 Agent：全套适用
- 私人助理：只用记忆写入纪律部分

**关键发现**:
- 3/12 反思失控：一天 8 次，内容高度重复
- 信息密度 > 信息量
- 事件驱动 > 时间驱动

---

### 2026-05-09: AI Agent 安全与基础设施情报
**文件**: [2026-05-09-ai-agent-safety-infra-intel.md](./2026-05-09-ai-agent-safety-infra-intel.md)

**核心内容**:
- ✅ OpenAI CoT 监控机制分析
- ✅ Anthropic 宪法对齐方案
- ✅ NVIDIA/LangChain/OpenRouter 安全趋势

**适用场景**:
- AI Agent 安全架构设计
- Agent 基础设施安全评估

---

### 2026-05-06: Agent Verification 赛道深挖
**文件**: [2026-05-06-agent-verification-deep-dive.md](./2026-05-06-agent-verification-deep-dive.md)

**核心内容**:
- ✅ Agent 验证竞品矩阵
- ✅ 关键开源项目
- ✅ 行业趋势与漏洞分析

**适用场景**:
- Agent 安全评估
- Agent 测试框架选型

---

### 2026-03-19: Bot 记忆系统规范 v1.1（2026-05-27 更新）
**文件**: [2026-03-19-bot-memory-system-spec.md](2026-03-19-bot-memory-system-spec.md)

**v1.1 核心改进（2026-05-27）**:
- ✅ **Agent 承诺追踪闭环**：WAL 触发条件扩展至 agent 自身的时间承诺
- ✅ **SESSION-STATE 激活**：新增 L1.5a 承诺追踪机制，agent 承诺必须落盘
- ✅ **晨报对账**：每日晨报增加「昨日计划未执行」段落，对比 SESSION-STATE 承诺与日记完成记录

**修复的核心问题**:
- v1.0 协议设计盲区：只有主人的话触发 WAL，agent 的承诺从未被强制记录
- 导致 agent 做出时间承诺后，session 一断就失忆，承诺无追踪、晨报不提
- 修复后形成闭环：承诺 → 落盘 → 跨 session 可追踪 → 晨报自动对账

---

## 🔄 更新日志

- 2026-05-27: Bot 记忆系统规范更新至 v1.1（Agent 承诺追踪闭环）
- 2026-05-10: 新增 AI Agent 安全情报、Agent Verification 等
- 2026-03-18: 新增 Claude Code Skills 8条沉淀原则
- 2026-03-15: 新增 OpenClaw Telegram 连接问题排查报告
- 2026-03-14: 新增 Agentic Checklist 实验总结
