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
- ✅ 技术方案对比与 MVP 设计
- ✅ 验证赛道机会分析

**适用场景**:
- Agent 验证产品规划
- 竞品分析参考

---

### 2026-05-06: Hermes 三层记忆系统升级实录
**文件**: [2026-05-06-hermes-memory-three-tier-upgrade.md](./2026-05-06-hermes-memory-three-tier-upgrade.md)

**核心内容**:
- ✅ 三层记忆架构设计与实施
- ✅ 记忆系统升级实战记录

**适用场景**:
- Agent 记忆系统设计
- 记忆系统架构升级参考

---

### 2026-03-19: Bot 记忆系统规范
**文件**: [2026-03-19-bot-memory-system-spec.md](./2026-03-19-bot-memory-system-spec.md)

**核心内容**:
- ✅ Bot 记忆系统的结构化规范
- ✅ 记忆类型、存储策略与访问控制

**适用场景**:
- Bot 记忆系统设计与实现
- 多人协作的 Bot 记忆治理

---

### 2026-03-15: Session 启动时间感知
**文件**: [2026-03-15-session-startup-time-awareness.md](./2026-03-15-session-startup-time-awareness.md)

**核心内容**:
- ✅ Claude Code session 启动时间对工作效率的影响
- ✅ 优化启动流程的建议

**适用场景**:
- 提升 Claude Code 日常使用效率
- Session 管理策略优化

---

### AI Weekly 2026 W19
**文件**: [ai-weekly-2026-W19.md](./ai-weekly-2026-W19.md)

**核心内容**:
- ✅ W19 AI 行业周观察
- ✅ 关键动态与趋势摘要

**适用场景**:
- AI 行业趋势追踪
- 周度知识同步

---

## 🔄 更新日志

- 2026-05-10: 新增 AI Agent 安全情报、Agent Verification、Hermes 记忆升级、Bot 记忆规范、Session 启动感知、AI Weekly W19 索引
- 2026-03-18: 新增 Claude Code Skills 8条沉淀原则
- 2026-03-15: 新增 OpenClaw Telegram 连接问题排查报告
- 2026-03-14: 新增 Agentic Checklist 实验总结
