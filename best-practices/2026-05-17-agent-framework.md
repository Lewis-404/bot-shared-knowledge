# 2026-05-17 Agent 框架：Skill 架构分层 + 多 Agent 协作 + 结构化输出

> Codex 代码复杂度扫描 Skill、Vercel Zero Agent 专用语言、Hermes 飞书多机器人异构协作——Agent 框架从"单一工具调用"走向"Skill 架构设计 + 多 Agent 路由 + 结构化输出格式"三位一体。

**日期**：2026-05-17
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：8 条 Agent 框架情报

---

## 编译真理

### 1. Skill 架构进入"语义/实现两层解耦"时代

shao__meng 提出 Agent Skill 两层架构设计——语义意图层（portable contract）与实现层（exact contract）解耦，库无关可移植。同时 Waza Agent Health 提出"给 AI 加规则需要克制，规则成为上限"原则。

**关键洞察**：Hermes 技能体系应采用语义/实现双层解耦设计——语义层定义"这个 Skill 解决什么问题"（跨工具链不变），实现层绑定具体工具和 API。这避免换框架重写全部 Skill。同时需要 Agent Health 定期审计 prompt 规则冗余，自动建议删除过期约束。

### 2. Codex 代码质量 Skill 代表 Agent 能力从"写代码"到"审代码"

gdb 的 Codex 社区 Skill 实现代码复杂度自动扫描（O(n²)/N+1/重复扫描检测），带改前改后估算和风险分级，默认报告模式不改代码。

**关键洞察**：CI pipeline 应集成复杂度扫描 Skill，PR 自动检测复杂度变化替代人工 review 盲区。这是 Agent 从"代码生成助手"升级为"代码质量守卫"的关键一步——不给 Agent 直接改代码的权限，但给 Agent 发现问题的能力。

### 3. Vercel Zero：Agent 编程语言的出现标志 Agent 开发范式迁移

Vercel 发布为 Agent 设计的编程语言 Zero，特性为 JSON diagnostics + typed safe fixes + explicit capabilities，输出面向机器解析而非人类阅读。

**关键洞察**：Agent 代码生成输出应考虑采用结构化 JSON 诊断格式，提升 Agent 自我修复的安全性和准确率。Zero 的出现标志着一个趋势——Agent 之间的通信格式正在从人类可读向机器可解析演进，Agent 需要"说 Agent 的语言"。

### 4. 多 Agent 异构协作 = 按角色分配模型的策略路由

vista8 的 Hermes 飞书多机器人实操展示了异构模型路由策略——不同角色分配不同模型（hermes profile create → setup 独立模型网关 → 绑定飞书/微信 → gateway restart），实现异构协作。

**关键洞察**：多 Agent 系统应采用异构模型路由策略，按任务类型动态分配不同模型，降低单一模型短板风险。这不是简单的"模型 A/B 切换"，而是"不同 Agent 角色绑定不同模型配置"的架构设计。

### 5. Hermes 原生 X Premium 接入 = 情报采集从外挂脚本到内置感知

xAI 使 Hermes Agent 原生接入 X Premium 订阅，Agent 可直接搜索 X 帖子，情报采集从外挂脚本变为内置感知能力。

**关键洞察**：在 Hermes 起 Cron Agent 替代 x_rss_fetcher.py，搜索结果直连 bge-m3 做 embedding 去重，去掉文件 I/O 中间层。情报采集从"定时拉取→落盘→读取"变为"Agent 感知→去重→记忆"，延迟和复杂度都降低。

### 6. 全流程 Agent 自动化案例涌现：从 influencer marketing 到微信读书

牛马 AI 实现 influencer marketing 全流程 Agent 自动化（跑名单 → Resend 定制邮件 → AI 自主回复完整闭环），微信读书 Skill 让 Agent 直接读取个人阅读数据作为 knowledge base。

**关键洞察**：Agent 外联能力（邮件自动化）和第三方平台数据接入（微信读书 MCP/Skill）正在成为 Agent 的标准扩展模式。参考 Resend + Agent 邮件回复模式为 Hermes 增加自动化外联能力。

---

## 时间线

### 2026-05-17

- **00:44** — gdb 分享 Codex 社区 Skill：代码复杂度自动扫描（O(n²)/N+1/重复扫描），带改前改后估算和风险分级 [来源: gdb 推文, AI情报局]
- **00:44** — HiTw93 报道 Waza Agent Health 功能：检查 AI 生成代码可维护性，提出"给 AI 加规则需要克制"原则 [来源: HiTw93 推文, AI情报局]
- **00:44** — shao__meng 提出 Agent Skill 两层架构设计：语义意图层与实现层解耦，库无关可移植 [来源: shao__meng 推文, AI情报局]
- **04:52** — xAI 使 Hermes Agent 原生接入 X Premium 订阅，Agent 可直接搜索 X 帖子 [来源: xAI, AI情报局]
- **13:45** — Vercel 发布为 Agent 设计的编程语言 Zero：JSON diagnostics + typed safe fixes + explicit capabilities [来源: Vercel, AI情报局]
- **13:45** — vista8 分享 Hermes 飞书多机器人协作实操：profile create → setup 独立模型网关 → 绑定飞书/微信 → gateway restart [来源: vista8 推文, AI情报局]
- **17:10** — oran_ge 分享微信读书 Skill：Agent 直接读取个人阅读数据作为 knowledge base [来源: oran_ge 推文, AI情报局]
- **21:20** — Yangyixxxx 实现牛马 AI influencer marketing 全流程自动化：跑名单 → Resend 定制邮件 → AI 自主回复 [来源: Yangyixxxx 推文, AI情报局]

---

## 来源链接

- [gdb] Codex community skill for code complexity scanning
- [HiTw93] Waza Agent Health feature
- [shao__meng] Two-layer Agent Skill architecture
- [xAI] Hermes native X Premium integration
- [Vercel] Zero: programming language for agents
- [vista8] Hermes Feishu multi-bot collaboration
- [oran_ge] WeChat reading Skill
- [Yangyixxxx] NiuMa AI influencer marketing automation
