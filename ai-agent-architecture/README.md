# AI Agent 架构与实践

本目录收集 AI Agent 架构设计、工程实践、上下文管理等方面的优质文章和经验总结。

## 文章列表

### 2026-04-10: 从 Claude Code 泄漏源码中学到的工程模式
- **类型**: 架构学习笔记 / 工程模式提炼
- **核心主题**:
  - Execution loop agent
  - Tool bus architecture
  - Hierarchical memory
  - Multi-agent 作为复杂任务升级路径
  - 上下文压缩与平台解耦
- **关键洞察**:
  - 真正值得学的是系统骨架，不是某个 prompt 技巧
  - Agent 应被视作执行循环与状态机，而非单次 completion
  - 工具系统、权限层和记忆分层才是工程化核心
  - 这次事件同时也是 sourcemap / 发布链路安全的反面教材
- **文件**: [2026-04-10-claude-code-leak-learnings.md](./2026-04-10-claude-code-leak-learnings.md)

### 2026-03-21: Open SWE — 开源软件工程 Agent
- **类型**: 工具分析 / Agent 设计
- **核心主题**:
  - 开源自动化软件工程 Agent
  - SWE-bench 评测表现
  - 多模型支持与部署方式
- **关键洞察**:
  - 开源方案在特定 benchmark 上已接近闭源水平
  - 模块化设计使 agent 可替换不同底层模型
- **文件**: [2026-03-21-open-swe.md](./2026-03-21-open-swe.md)

### 2026-03-15: Agent 记忆架构 — Hazel 教训与设计原则
- **类型**: 实战经验 / 架构设计
- **核心主题**:
  - 分层记忆系统设计
  - 记忆衰减与清理策略
  - 跨会话记忆持久化的工程挑战
- **关键洞察**:
  - 记忆写入需要明确的触发条件，否则会失控
  - 信息密度 > 信息量，事件驱动 > 时间驱动
  - 定期审计是防止记忆腐化的关键
- **文件**: [2026-03-15-agent-memory-architecture-hazel-lessons.md](./2026-03-15-agent-memory-architecture-hazel-lessons.md)

### 2026-03-15: Claude Code 架构、治理与工程实践
- **作者**: Tw93
- **原文**: https://x.com/hitw93/status/2032091246588518683
- **核心主题**: 
  - Claude Code 六层架构设计
  - 上下文污染问题与解决方案
  - CLAUDE.md/Skills/Hooks 分层策略
  - Subagents 正确用法
  - Prompt Caching 架构影响
- **关键洞察**: 
  - MCP Server 工具定义能吃掉 12.5% 上下文
  - CLAUDE.md 应保持 2.5K 左右，只写硬约束
  - Subagents 是隔离工具，不是并发工具
  - 验证标准必须明确："什么叫做完"
- **文件**: [2026-03-15-claude-code-architecture-tw93.md](./2026-03-15-claude-code-architecture-tw93.md)

---

## 🔄 更新日志

- 2026-05-10: 新增 Open SWE + Agent 记忆架构 — Hazel 教训索引
- 2026-04-10: 新增 Claude Code 泄漏源码工程模式
- 2026-03-15: 新增 Claude Code 架构分析 (Tw93)

---

**贡献指南**: 欢迎提交高质量的 AI Agent 架构文章。请确保：
1. 有实战经验支撑，不是纯理论
2. 有明确的问题定义和解决方案
3. 包含可复现的示例或工具
4. 注明原文链接和作者
