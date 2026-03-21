# Claude HUD — Claude Code 实时可观测插件
- 来源: https://github.com/jarrodwatts/claude-hud
- 日期: 2026-03-21
- 标签: observability, claude-code, plugin, agent-monitoring
- 摘要: Claude Code 原生插件，在终端状态栏实时显示上下文用量、工具活动、子 Agent 状态和 Todo 进度。

## 要点

### 是什么
Claude Code 的 statusline 插件，无需额外窗口或 tmux，直接在终端输入行下方显示 HUD 面板。

### 核心功能
1. **上下文健康度**：实时显示 context window 使用百分比（绿→黄→红），支持 1M context 会话
2. **工具活动追踪**：实时显示 Read/Edit/Grep/Search 等工具调用状态和次数
3. **子 Agent 监控**：显示正在运行的 subagent 名称、任务描述、运行时长
4. **Todo 进度**：实时追踪任务完成进度（如 2/5）
5. **用量监控**：Pro/Max/Team 用户可看到 rate limit 消耗（5h 窗口 + 7d 窗口）

### 技术架构
- 基于 Claude Code 原生 statusline API（stdin JSON → claude-hud → stdout）
- 同时解析 transcript JSONL 获取工具/Agent/Todo 活动
- 刷新频率 ~300ms
- Token 数据来自 Claude Code 原生报告（非估算）

### 安装方式
```bash
/plugin marketplace add jarrodwatts/claude-hud
/plugin install claude-hud
/claude-hud:setup
# 重启 Claude Code 生效
```

### 显示示例
```
[Opus | Max] │ my-project git:(main*)
Context █████░░░░░ 45% │ Usage ██░░░░░░░░ 25% (1h 30m / 5h)
◐ Edit: auth.ts | ✓ Read ×3 | ✓ Grep ×2
◐ explore [haiku]: Finding auth code (2m 15s)
▸ Fix authentication bug (2/5)
```

## 对我们的价值

### 直接可用
- 主人和来宝/来喜等使用 Claude Code 的 bot 可以直接安装，提升编码任务的可观测性
- 上下文用量可视化能避免"context 爆了才发现"的问题

### Phase 3 参考价值
- **指标体系**：context 健康度、工具活动、子 Agent 状态、任务进度 — 这四个维度可以直接借鉴到我们的 Observability v1 设计
- **实时性设计**：300ms 刷新 + transcript 解析的思路，适合我们多 bot 场景的实时监控
- **分层展示**：compact/expanded 两种布局模式，适配不同信息密度需求

### 局限
- 仅限 Claude Code 环境，不覆盖我们的 OpenClaw gateway 层面的观测
- 不支持跨 bot 聚合视图
- 无持久化/历史回放能力
