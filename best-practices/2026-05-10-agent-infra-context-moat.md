# 2026-05-10 Agent 基础设施与上下文层护城河 — 情报合成

> 上下文层取代模型成为 Agent 真正护城河；多插件编排 + 外部状态存储 = 长时 Agent 的落地配方；cc-router 模式解决多 Agent 成本调度。

**日期**：2026-05-10
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）

---

## 编译真理

### 1. 上下文层 = Agent 唯一真正护城河

Jerry Liu (LlamaIndex) 提出关键洞察：

- 2026 年 Agent 的唯一真正护城河可能不是模型，而是**上下文层**
- 模型和 Agent 抽象在固化——所有人最终都能调用差不多的模型
- 差异化来自：系统记录、网页上下文、文档上下文、跨会话记忆
- MCP 工具 vs 沙箱原语的「工具层」仍在博弈，但上下文层是更底层的壁垒

**可落地点**：Hermes 的 bge-m3 向量记忆体系正是上下文层核心资产；优先完善上下文索引和跨会话检索能力；把 context retrieval 质量作为 Agent 质量的关键指标而非 afterthought。

### 2. 多插件编排 + 外部状态存储 = 长时 Agent 落地配方

Greg Brockman 展示 Codex 真实报销场景，全流程 20 分钟零人工干预：

- **四插件编排**：Drive 存状态、Sheets 做任务面板、Gmail 抓发票、Chrome 填表单
- **状态落外部而非内部记忆**：任务进度写 Spreadsheet，每个 Agent 读任务面板 + 写结果
- **故障恢复**：挂掉重跑不丢进度——状态在 Sheet 里，不在 Agent 的内存里

推文中重复出现 3 次（不同 KOL 引用），说明行业对「多工具串联 + 外部状态」的模式高度认可。

**可落地点**：多 Agent 系统必须设计共享状态层（外部结构化存储），避免每次调用无状态导致无法执行多步长任务；Hermes cron Agent 可用 Google Sheets 替代 Redis 做轻量状态存储。

### 3. 长时 Agent 基础设施：GPT-Realtime-2 的流式上下文

Greg Brockman 展示 GPT-Realtime-2 能力：

- 浏览器实时语音翻译 + Codex 同上下文工作
- Agent 操作发生在 streaming/multimodal 实时上下文中而非静态文档
- 暗示 Agent 架构应从「一次性静态加载」转向「持续流式上下文更新」

**可落地点**：Agent 设计应支持流式上下文更新，不假设一次性静态加载；为 Hermes 的长时间运行 cron Agent 增加中间状态推送能力。

### 4. LLM Wikis + HTML Artifacts = Agent 知识交互层

elvis/omarsar0 提出 Agent 知识工作流新范式：

- LLM Wikis 做知识沉淀（Markdown 层）
- HTML Artifacts 做交互层（人可操作的界面）
- Agent ↔ Artifact 双向通信：MD 喂给 Agent，HTML 给人做操作面板
- 这与 bot-shared-knowledge 的纯 MD 架构形成对比——可升级方向明确

**可落地点**：bot-shared-knowledge 参考此架构，MD 层做知识库，HTML Artifacts 做交互 Dashboard；Agent 产出不再只是文本，而是可操作界面。

### 5. cc-router 多 Agent 套餐调度模式

Geek 发布 cc-router：

- 实现多 Claude Code 套餐虚拟 Plan 合并
- 轮询调度 + 限流自动切换
- 把分散额度利用率拉满——先优化调度层再扩容

**可落地点**：多 Agent 协作先优化调度层再扩容；用路由层屏蔽底层额度差异；Hermes 多模型调度可参考轮询+限流自动切换模式。

---

## 时间线

### 2026-05-10

- **00:08** — Jerry Liu (LlamaIndex) 提出上下文层是 Agent 唯一真正护城河，模型和 Agent 抽象在固化 [来源: AI情报局, Jerry Liu/LlamaIndex]
- **04:19** — elvis/omarsar0 提出 LLM Wikis + HTML Artifacts 做 Agent 知识交互层，Agent↔Artifact 双向通信 [来源: AI情报局, elvis/omarsar0]
- **04:19** — Greg Brockman 展示 GPT-Realtime-2：浏览器实时语音翻译 + Codex 同上下文工作 [来源: AI情报局, Greg Brockman/OpenAI]
- **08:30** — Greg Brockman 展示 Codex 报销全流程：Drive+Sheets+Gmail+Chrome 四插件编排，20 分钟零人工干预 [来源: AI情报局, gdb/Codex]
- **12:34** — Greg Brockman 报销案例二次传播：行业对多插件协作+外部状态存储模式高度认可 [来源: AI情报局, Greg Brockman]
- **16:45** — Greg Brockman 报销案例三次传播：强调状态持久化落 Spreadsheet，挂掉重跑不丢进度 [来源: AI情报局, Greg Brockman/Codex]
- **20:48** — Geek 发布 cc-router：多 Claude Code 套餐虚拟 Plan 合并，轮询调度+限流自动切换 [来源: AI情报局, Geek/cc-router]

---

## 来源链接

- Jerry Liu / 上下文层护城河: https://x.com/jerryjliu0
- elvis/omarsar0 / LLM Wikis + HTML Artifacts: https://x.com/omarsar0
- Greg Brockman / GPT-Realtime-2 + Codex 报销: https://x.com/gdb
- Geek / cc-router: https://x.com/geekbb
