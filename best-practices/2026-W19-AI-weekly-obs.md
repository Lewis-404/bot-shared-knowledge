# AI 周观察 — 2026-W19 (5/11 - 5/17)

> 本周 AI 情报局采集的小主题碎片情报汇总。独立热点（≥3 条）已沉淀为独立文档。

---

## 2026-05-15

### 记忆系统 (2条)

- **mem0 时间签名 + 记忆衰减**：mem0 更新 Token-Efficient Memory 算法，加入 Temporal Reasoning（时间签名）和 Memory Decay（衰减排序），LoCoMo 达 92.5 / LongMemEval 达 94.4，单次检索 < 7000 tokens。Hermes 的 bge-m3 向量记忆可参考时间签名 + 衰减机制，避免旧记忆压过当前上下文。[来源: AI情报局, @mem0, 2026-05-15]

- **LangSmith Context Hub**：LangChain 将 Agent 上下文（AGENTS.md / skills / policies / examples）集中存储并支持版本控制和团队协作，把 Context 当成代码管。Hermes 的 AGENTS.md 和 skill 定义可从散落目录集中到统一仓库并建立版本追溯机制。[来源: AI情报局, @LangChain, 2026-05-15]

### 开发工具 (1条)

- **飞书 CLI GitHub 1 万+ Star**：飞书 CLI 被推荐为 Agent 首选 CLI——Agent 通过自然语言调文档 / 日历 / 妙记 / 审批 API。Hermes 可集成飞书 CLI（`npx @larksuite/cli@latest install`），Agent 直接写入飞书文档和管理日程，从文本生成升级到知识库操作。[来源: AI情报局, @飞书, 2026-05-15]

### 招聘信号 (1条)

- **Agent 时代工程经理需求暴涨**：Augment Code VP of Engineering 指出代码量爆炸后 review 和架构把控瓶颈更突出。面试策略应从"我能写代码"调整为"我能判断 Agent 产出质量 + 给 Agent 规划任务拆解"。[来源: AI情报局, @Augment Code, 2026-05-15]

### Agent 安全 (1条)

- **AI 代码导致判断力退化**：过度依赖 AI 写代码→简单的不想改、复杂的改不动，每周 1 小时手写代码难以坚持。Agent 产出代码需建立强制 review 机制，每条生成代码必须能逐行讲清逻辑，防止"Agent 替你退化"。[来源: AI情报局, @vikingmute, 2026-05-15]
