# Tools 索引

这里收录工具使用经验和实战指南。

---

## 📚 内容列表

### 2026-03-14: CLI-Anything 使用指南
**文件**: [2026-03-14-cli-anything-guide.md](2026-03-14-cli-anything-guide.md)

**核心内容**:
- ✅ 什么是 CLI-Anything（让任何软件 Agent 化）
- ✅ 安装方法（通过 Claude Code 插件）
- ✅ 使用流程（生成 → 安装 → 测试 → 使用）
- ✅ 在 OpenClaw 中使用（通过 sessions_spawn 和 exec）
- ✅ 配置要点（路径、无头模式、权限）
- ✅ 最佳实践（JSON 输出、错误处理、批量操作）

**已支持软件**:
- GIMP（图像编辑）
- Blender（3D 建模）
- LibreOffice（办公套件）
- FFmpeg（视频处理）
- 等 11 个专业软件

**适用场景**:
- 让 OpenClaw Agent 能直接调用专业软件
- 为自己的软件生成 Agent 友好的 CLI
- 扩展 Agent 的能力边界

---

### 2026-03-24: OpenClaw 远程服务器代理接入执行流程（proxychains + systemd）
**文件**: [2026-03-24-openclaw-remote-proxychains-systemd.md](./2026-03-24-openclaw-remote-proxychains-systemd.md)

**核心内容**:
- ✅ 故障识别条件（service active 但 Discord fetch failed）
- ✅ proxychains 配置与 systemd drop-in ExecStart 注入
- ✅ 重启与 4 项验收命令
- ✅ 一键回滚方案与常见坑位
- ✅ 迁移到新机器时的变量替换模板

**适用场景**:
- Linux 远程主机 OpenClaw 进程可启动但 Discord 离线
- 需要最小改动（仅 OpenClaw 进程走代理）
- 希望可快速复制、可快速回滚的修复流程

---

### 2026-03-21: Claude HUD — 开发者工具面板
**文件**: [2026-03-21-claude-hud.md](./2026-03-21-claude-hud.md)

**核心内容**:
- ✅ Claude Code HUD 功能介绍
- ✅ 实时监控 token 消耗、工具调用
- ✅ 配置与自定义面板

**适用场景**:
- 监控 Claude Code 运行时状态
- 优化 token 预算

---

### 2026-03-21: Respan AI 可观测性
**文件**: [2026-03-21-respan-ai-observability.md](./2026-03-21-respan-ai-observability.md)

**核心内容**:
- ✅ AI Agent 可观测性平台介绍
- ✅ 追踪、监控与调试 Agent 行为
- ✅ 与传统 APM 的差异

**适用场景**:
- AI Agent 生产环境监控
- Agent 行为审计与调试

---

## 🔄 更新日志

- 2026-05-10: 新增 Claude HUD + Respan AI 可观测性索引
- 2026-03-25: 新增 OpenClaw 远程服务器代理接入执行流程（proxychains + systemd）
- 2026-03-14: 新增 CLI-Anything 使用指南

## Agency Agents × OpenClaw 集成

**文件**: [2026-03-15-agency-agents-openclaw-integration.md](./2026-03-15-agency-agents-openclaw-integration.md)

**核心内容**:
- 147 个专业 AI Agent 的开源集合
- 按需精选安装指南（避免配置污染）
- 全栈工程师、产品经理、营销人员、设计师推荐清单
- 实战案例：前端性能优化、后端架构设计、代码审查
- 与 Claude Code 的对比分析

**关键洞察**:
- 一次性安装 147 个 Agent 会导致配置文件臃肿和 Agent 列表噪音
- 推荐按专业领域精选 5-15 个核心 Agent
- 每个 Agent 都有明确的个性、工作流程、交付物模板和成功指标
- 通过 `sessions_spawn` 调用专业 Agent 处理特定任务

**适用场景**:
- OpenClaw 用户需要专业领域的 AI 助手
- 希望保持配置简洁、按需扩展
- 需要标准化的工作流程和交付物模板
