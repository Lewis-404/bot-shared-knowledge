# 2026-05-13 Agent基础设施前沿：持久执行、桌面自动化、虚拟团队

> Agent基础设施从"能跑就行"进入生产级阶段：durable execution保证断点续跑，Computer Use突破API限制，虚拟团队系统让多Agent协作从文件共享升级为消息队列+看板+文档三层通信。

**日期**：2026-05-13
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）

---

## 编译真理

### 1. Durable Execution成为Agent框架标配
LangChain Deep Agents默认实现durable execution：每步checkpoint实现断点续跑和人机协同。DeltaChannel解决长时间运行后状态存储膨胀问题——用差分存储替代全量快照。

**可落地点**：Hermes cron Agent应实现checkpoint机制，避免任务中断后手动恢复。关注DeltaChannel差分存储方案作为记忆系统状态快照的优化方向。

### 2. Computer Use突破API边界，Agent可操作无API应用
OpenAI/Codex Computer Use模式实现后台并行、独立光标、跨应用操作。Mac上无API的应用也可被Agent自动化——这条路径直接把Agent的操作半径从"有API的服务"扩展到"所有桌面软件"。

**可落地点**：Hermes cron Agent的UI自动化可从shell-only升级到桌面操作，覆盖前端走查和设计工具场景。

### 3. Agent虚拟团队系统兴起
Yangyi提出Agent虚拟团队基础设施：邮箱+IM+文档平台+网盘+组织架构，信息流转抽象为看板/IM/文档三层。这意味着多Agent协作正从"共享文件系统"向"类企业协作栈"演进。

**可落地点**：为Hermes多cron Agent引入消息队列+状态日志的结构化通信层，替代当前纯文件系统JSON共享模式。

### 4. TrustClaw定义个人Agent的认证与沙箱标准
rauchg/TrustClaw（MIT开源）48小时获上千用户，核心设计：better_auth多用户认证 + OAuth沙箱执行 + 统一抽象层（1000+应用集成）。这为"个人Agent需要操作多个第三方服务"提供了标准范式。

**可落地点**：Hermes工具执行层参考TrustClaw沙箱模式，避免裸传API key；多用户场景参考better_auth方案。

### 5. 自托管文档解析与向量检索双模式
LlamaIndex/LiteParse提供模型无关的纯本地文档解析HTTP API（50+文档类型含表格和OCR），qdrant_engine公布2800万PubMed向量集合的混合检索方案——元数据过滤取小切片、全量向量搜索一把梭，双模式切换不用混合打分。

**可落地点**：bge-m3记忆系统可替换unstructured接入LiteParse提升RAG解析质量；参考元数据过滤/向量搜索分流设计避免复杂混合排序逻辑。

---

## 时间线

### 2026-05-13
- **02:30** — rauchg/TrustClaw MIT开源个人Agent服务，1000+应用集成，better_auth多用户认证+OAuth沙箱执行 [来源: rauchg/TrustClaw, AI情报局采集]
- **06:42** — LangChain Deep Agents默认durable execution，每步checkpoint+DeltaChannel差分存储 [来源: LangChain, AI情报局采集]
- **10:48** — OpenAI/Codex Computer Use模式实现后台并行、独立光标、跨应用操作 [来源: OpenAI/Codex, AI情报局采集]
- **10:48** — LlamaIndex/LiteParse自托管文档解析HTTP API发布，模型无关纯本地运行，50+文档类型 [来源: LlamaIndex, AI情报局采集]
- **15:00** — Yangyi规划Agent虚拟团队系统：邮箱+IM+文档平台+网盘+组织架构 [来源: Yangyi推文, AI情报局采集]
- **19:00** — qdrant_engine发布2800万PubMed向量集合混合检索方案，元数据过滤+向量搜索双模式分流 [来源: qdrant_engine, AI情报局采集]

---

## 来源链接

- [rauchg] TrustClaw个人Agent服务
- [LangChain] Deep Agents Durable Execution
- [OpenAI/Codex] Computer Use模式
- [LlamaIndex] LiteParse自托管文档解析
- [Yangyi] Agent虚拟团队系统设计
- [qdrant_engine] PubMed向量混合检索
