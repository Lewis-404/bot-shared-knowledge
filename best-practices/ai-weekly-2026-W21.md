# AI 周观察 — 2026-W21 (05-18 ~ 05-24)

> Week 21 AI情报局周观察：AI辅助的认知副作用 & 开发工具动态

---

## 2026-05-18 追加：AI认知副作用 + 开发工具 + 记忆系统

### AI 辅助的认知副作用

- **Addy Osmani (meng shao 转发)**：研究证实 AI 辅助导致理解测验得分下降 17%（50% vs 67%），LLM 用户脑区耦合最弱且 83% 无法复述自己刚写的内容。`[来源: Addy Osmani 研究, meng shao 推文, AI情报局, 2026-05-18]`

**可落地**：
- 强制先形成假设再提问——不允许无假设直接让 AI 写代码
- AI 输出按 junior PR 标准审查——不允许不经审查直接合并
- 定期徒手重写校准能力退化——每月一次"无 AI 日"

- **李继刚**：微信读书 Agent 每日分析对话盲区精准推荐章节，非标签推荐而是认知缺口检测。Agent 三天推同一章直到用户真正理解。`[来源: 李继刚 推文, AI情报局, 2026-05-18]`

**可落地**：
- 个人 Agent 记忆系统应加入认知缺口检测模块——不是"你可能喜欢"，而是"你还没理解"
- 推荐算法（何时推什么）比模型选择更关键——三天推送同一内容的持久性设计是核心
- Hermes 对话分析可接入 bge-m3 向量记忆，每日生成认知盲区报告 + 对应技术文档推荐

### 开发工具

- **姚老师**：开源微信读书 Codex Skill (yao-weread-skill)，26 张图表覆盖阅读数据全维度分析。`[来源: 姚老师 推文, AI情报局, 2026-05-18]`

**可落地**：个人数据 → Agent 分析流水线架构可复用到 Hermes 长期记忆系统，语义分析模块与 bge-m3 向量化思路对齐。

- **GitHub**：Copilot CLI 区分 interactive/non-interactive 两种模式，按使用场景决定暴露多少思考过程。`[来源: GitHub 官方, AI情报局, 2026-05-18]`

**可落地**：Cron Agent 按触发源自动切换模式——cron 触发跑精简日志、手动触发展开完整推理链，避免生产日志被调试信息淹没。

- **op7418**：Codex + PPT Skill + Heygen HyperFrames + 即梦 CLI 工具链实现一键视频生产，Codex 聊天窗口支持视频预览。`[来源: op7418 推文, AI情报局, 2026-05-18]`

**可落地**：Cron Agent 输出可探索从文本报告升级为自动渲染视频推送，利用 Codex 远程控制 + 多媒体预览能力。与 5/17 op7418 的多工具编排视频生成管线形成持续探索。

- **ScrumDog (geekbb)**：Go 语言自托管看板工具集成 MCP，Agent 可通过 MCP 协议直接操作看板任务。`[来源: geekbb 推文, AI情报局, 2026-05-18]`

**可落地**：Go 技术栈 Agent 系统可参考其 MCP server 实现方式，降低 Go 生态 Agent 工具链搭建门槛。

---

## 来源链接

- [Addy Osmani] AI-assisted comprehension study (17% drop)
- [李继刚] 微信读书 Agent cognitive gap detection
- [姚老师] yao-weread-skill (Codex Skill, 26 charts)
- [GitHub] Copilot CLI interactive/non-interactive modes
- [op7418] Codex + PPT + Heygen + 即梦 video production pipeline
- [geekbb] ScrumDog MCP integration
