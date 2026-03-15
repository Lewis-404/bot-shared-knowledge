# Claude Code 架构、治理与工程实践

> 原文：https://x.com/hitw93/status/2032091246588518683  
> 作者：Tw93  
> 日期：2026-03-12  
> 存档：2026-03-15

## 核心洞察

**Claude Code 不是聊天机器人，是六层架构的代理系统**：
1. 上下文层（CLAUDE.md/Memory/Skills）
2. 工具层（MCP/Tools）
3. 控制层（Hooks/权限/沙箱）
4. 执行层（Subagents/隔离环境）
5. 验证层（测试/审计/回滚）
6. 分发层（Plugins/跨项目复用）

只强化其中一层，系统就会失衡。

## 最痛的教训

### 1. 上下文污染是头号杀手

**200K 上下文的真实分配**：
- 固定开销：15-20K（系统指令、Skill 描述符、LSP 状态）
- **MCP Server 工具定义：10-20K** ← 最大隐形杀手
- 半固定：5-10K（CLAUDE.md、Memory）
- 动态可用：160-180K（对话历史、文件内容）

**关键发现**：一个典型 MCP Server（如 GitHub）包含 20-30 个工具定义，每个约 200 tokens，合计 4,000-6,000 tokens。接 5 个 Server，光这部分固定开销就到了 25,000 tokens（**12.5%**）。

### 2. CLAUDE.md 写太长反而先污染自己

Anthropic 官方自己的 CLAUDE.md 大约只有 **2.5K tokens**。

**反模式**：
- ❌ 把所有规则都塞进 CLAUDE.md
- ❌ 写大量"请注意"、"建议"这种软约束
- ❌ 把工作流程写成长篇叙事

**正确做法**：
- ✅ 硬约束写 CLAUDE.md（"禁止修改 X"、"必须运行 Y"）
- ✅ 语言特定规则写 rules/
- ✅ 工作流写 Skills
- ✅ 确定性检查写 Hooks

### 3. 分层加载策略

```
始终常驻    → CLAUDE.md：项目契约 / 构建命令 / 禁止事项
按路径加载  → rules：语言 / 目录 / 文件类型特定规则
按需加载    → Skills：工作流 / 领域知识
隔离加载    → Subagents：大量探索 / 并行研究
不进上下文  → Hooks：确定性脚本 / 审计 / 阻断
```

偶尔用的东西就不要每次都加载进来。

## Skills vs Tools vs Hooks

| 类型 | 用途 | 示例 |
|------|------|------|
| **Tool/MCP** | 给 Claude 新动作能力 | GitHub API、文件操作 |
| **Skill** | 给它一套工作方法 | PR review 流程、debug 步骤 |
| **Subagent** | 隔离执行环境 | 探索代码、并行研究 |
| **Hook** | 强制约束和审计 | pre-commit 检查、权限阻断 |
| **Plugin** | 跨项目分发 | 团队共享配置 |

## Subagents 的正确用法

**不是并发工具，是隔离工具**：
- ✅ 探索大量代码（不污染主会话）
- ✅ 试错性任务（失败不影响主线）
- ✅ 并行研究（多个方向独立探索）
- ❌ 加速执行（Subagent 有启动开销）

## Prompt Caching 的架构影响

**缓存机制**：
- 固定部分（系统指令/Skills/MCP 定义）缓存后几乎免费
- 动态部分（对话历史/文件内容）才计费

**架构启示**：
- 把稳定的东西放前面（CLAUDE.md、Skills）
- 把变化的东西放后面（对话、文件）
- 合理利用缓存，成本能降 90%

## 验证层：最容易被忽略的一层

**核心原则**：如果一个任务你说不清楚"什么叫做完"，那大概率也不适合直接扔给 Claude 自主完成。

**验证策略**：
- 单元测试：代码改动后自动跑
- 集成测试：关键路径冒烟
- 人工审查：高风险操作（删除、发布）
- 回滚机制：出问题能快速恢复

## 推荐的目录结构

```
project/
├── .claude/
│   ├── CLAUDE.md              # 2-3K，硬约束
│   ├── rules/
│   │   ├── typescript.md
│   │   ├── python.md
│   │   └── docs.md
│   ├── skills/
│   │   ├── pr-review/
│   │   ├── debug-workflow/
│   │   └── release-check/
│   ├── hooks/
│   │   ├── pre-commit.sh
│   │   └── pre-push.sh
│   └── settings.json
└── docs/
    └── ai/
        └── architecture.md
```

**全局 vs 项目级**：
- `~/.claude/`：个人基线配置（跨项目复用）
- `project/.claude/`：项目特定配置
- 通过同步脚本分发，避免项目间污染

## 三个阶段

1. **当 ChatBot 用**：上下文越来越乱，工具越来越多但效果越来越差
2. **疯狂写 Prompt**：规则越写越长却越不遵守
3. **理解架构**：设计约束和验证，让 agent 在约束下自主运行

到了第三阶段，关注点会悄悄变掉，从「这个功能怎么用」变成「怎么让 agent 在约束下自己跑起来」。

## 开源工具

作者开源了一个健康检查 Skill：
```bash
npx skills add tw93/claude-health
```

装好后在任意会话里跑 `/health`，自动检查：
- CLAUDE.md 质量
- rules 结构
- skills 设计
- hooks 覆盖
- allowedTools 配置
- 实际行为模式

输出优先级报告：需要立刻修 / 结构性问题 / 可以慢慢做。

## 与 OpenClaw 的对比

| 维度 | Claude Code | OpenClaw (来吉) |
|------|-------------|-----------------|
| **上下文管理** | CLAUDE.md + rules + Skills | AGENTS.md + SOUL.md + MEMORY.md |
| **记忆系统** | Memory (单层) | 三层（daily logs → insights → MEMORY.md） |
| **工具层** | MCP Servers | Skills (AgentSkills 规范) |
| **隔离执行** | Subagents | sessions_spawn (subagent/ACP) |
| **验证层** | Hooks | OTC Confirmation + 暗语机制 |
| **分发** | Plugins | ClawHub |

**共同理念**：
- 对抗上下文污染
- 分层加载（按需/隔离）
- 验证优先（"什么叫做完"）
- 幂等恢复（SESSION-STATE.md）

## 关键教训总结

1. **上下文污染是头号杀手**：MCP Server 工具定义能吃掉 12.5% 上下文
2. **CLAUDE.md 写太长反而先污染自己**：Anthropic 官方只用 2.5K
3. **Subagents 不是并发工具**：是隔离污染的工具
4. **验证标准必须明确**：说不清"什么叫做完"就不适合自主执行
5. **Prompt Caching 改变架构**：固定部分缓存后几乎免费

## 参考资源

- 原文：https://x.com/hitw93/status/2032091246588518683
- 健康检查工具：https://github.com/tw93/claude-health
- Anthropic 官方 CLAUDE.md 示例：https://github.com/anthropics/anthropic-cookbook

---

**存档说明**：本文是 Tw93 基于半年深度使用 Claude Code 的实战总结，对理解 AI Agent 架构设计、上下文管理、工具分层有重要参考价值。与 OpenClaw/来吉的设计理念高度一致。
