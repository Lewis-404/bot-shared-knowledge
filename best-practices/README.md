# Best Practices 索引

这里收录经过实战验证的最佳实践和实验总结。

---

## 📚 内容列表

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

## 🔄 更新日志

- 2026-03-15: 新增 OpenClaw Telegram 连接问题排查报告
- 2026-03-14: 新增 Agentic Checklist 实验总结
