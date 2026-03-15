# AI Agent 架构与实践

本目录收集 AI Agent 架构设计、工程实践、上下文管理等方面的优质文章和经验总结。

## 文章列表

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

**贡献指南**: 欢迎提交高质量的 AI Agent 架构文章。请确保：
1. 有实战经验支撑，不是纯理论
2. 有明确的问题定义和解决方案
3. 包含可复现的示例或工具
4. 注明原文链接和作者
