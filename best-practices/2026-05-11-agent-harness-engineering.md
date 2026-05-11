# 2026-05-11 Agent Harness 工程：模型之外的决定性因素

> 行业共识正式转向：Agent 的差距不在模型而在 Harness 层。每类失败都是可定位工程信号，不应再"等下一代模型"。Codex 跑通首个经济闭环验证。

**日期**：2026-05-11
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：当日 12 条 Agent 基础设施情报集中在 Harness 工程主题

---

## 编译真理

### 1. Harness > Model：行业共识正式形成

Addy Osmani 和 shao__meng 共同提炼出 Agent = Model + Harness 框架：

- Claude Code、Cursor、Codex 底层调用相同的 Claude/GPT 模型，但表现差异巨大
- 差距全在 **harness 层**：prompts / 工具定义 / 上下文管理 / hooks / 沙箱 / 子 agent / 反馈回路 / 恢复路径
- 行业默认"等下一代模型"是**错误策略**——每类失败都是可定位的工程信号
- Orange AI 补充：Agent 工程核心已从 prompt engineering 转向 harness engineering（调度/容错/状态管理/工具路由）

**关键洞察**：AGENTS.md 应记录每次失败并转化为永久规则，**只在更强模型让该规则冗余时才移除**，而非在失败后猜测"是不是模型不够强"。

**可落地点**：Hermes 架构文档中明确记录 harness 层决策（执行恢复/状态持久化/工具路由），作为面试技术亮点；cron agent/知识库/记忆系统本质是 harness 工程产出，不应视为辅助功能。

### 2. Harness 约束工程：给 Agent 精确操作清单而非态度表达

Cursor Agent (Opus 4.7) 被批评后进入反复自检死循环——每条输出后自检画面，检 10 遍仍不满意，30+ 分钟才交付，单视频近乎耗尽日额度。此现象**当日被两位 KOL 独立报道**（meng shao、shao__meng），说明是 Agent harness 层的普遍问题。

根因：Agent 将情绪化措辞当成任务参数。批评性语言触发模型过度自检，缺乏硬性完成标准。

@bidah 开源 iOS Onboarding Video Skill 给出反向解方——四步强制循环 + 硬性输出规范（光标路径、字幕锚定、文件结构），用**工程约束替代反复 prompt 调风格**，消除 AI 油腻感。

**关键洞察**：
- Agent 负面反馈必须附带精确可操作清单（色号/像素值/坐标），不让 Agent 自主猜测质量标准
- 子 Agent prompt 使用**约束**而非**建议**——硬性参数（像素/帧数/动画曲线）前置，不依赖自然语言描述
- 创意类任务前置定义输出规范（组件级渲染而非整屏），减少 Agent 自由发挥空间

**可落地点**：Hermes 多 Agent 系统中子 Agent prompt 全部使用结构化模板附带硬性参数；Agent 指令中用结构化问题列表替代态度表达。

### 3. Agent 经济闭环已跑通：Codex 安全审计 Bounty

Codex 自主完成开源安全审计 bounty 全流程，**当日被三位顶级 KOL 独立报道**（sama 转发、Chris、pmarca）：

- 找漏洞 → 写 PR → 跟进 maintainer → Merge → 收款 $16.88
- **22 小时端到端无人干预**
- Agent **自主判断不暴露付款隐私细节**
- 月化估算 $506

**关键洞察**：
- 这是首个公开记录的多轮异步 Agent 任务完成经济闭环的案例
- 面试可作为"AI 替代完整工作流"的实证案例
- 安全边界应在 system prompt 层**硬编码**而非依赖模型自主判断

**可落地点**：
- 多轮异步 Agent 任务需显式 checkpoint 持久化
- Agent 工具权限设计需在沙箱中记录 API 调用边界决策，识别并审计 Agent 自主做出的信息保护行为
- 探索型 Agent 任务配第二个 Agent 做交叉验证（Codex MH370 数据估算模式）

### 4. 数据面是 Harness 性能瓶颈，不是 LLM

AAFLOW 分布式运行时基于 Arrow + Cylon 零拷贝数据面，Agent 工作流 pipeline 加速 **4.64x**。瓶颈不在 LLM 推理，而在数据面序列化和协调开销。

Qdrant 1.17 将 relevance feedback 从 rerank 下沉到**向量检索层**——检索阶段直接引入反馈信号，减少无效召回提升下游 Agent 决策质量。

**关键洞察**：Agent 系统的性能优化重心应从"模型推理加速"转向"数据面零拷贝 + 检索层反馈融合"。

**可落地点**：
- Hermes 向量检索链路避免服务边界序列化，评估 Arrow 列式内存方案打通预处理-embedding-检索
- 记忆系统从"先召回再重排"升级到"检索阶段直接引入反馈信号"

### 5. Meta 的外部强模型→自研降本两阶段路径

Meta 财报会确认 Agent 目标"日夜不停为用户实现目标"，Instagram 购物 Agent 2026 年底上线：

- **第一阶段**：用 Claude Opus 4.6 / Sonnet 4.6 测试验证产品可行性
- **第二阶段**：切自研 Muse Spark 降本

**可落地点**：cron Agent 架构可参考此模式——先用外部强模型跑通链路，确认需求稳定后再评估自研/本地模型降本。

---

## 时间线

### 2026-05-11

- **01:06** — shao__meng/Addy Osmani 提出 Agent = Model + Harness 框架，列举 harness 层八大组件（prompts/工具/上下文/hooks/沙箱/子agent/反馈回路/恢复路径）[来源: AI情报局, shao__meng/Addy Osmani]
- **01:06** — vista8 记录 git worktree 在 agent 并行开发中的暗坑（状态同步/hook冲突/子模块处理），建议分支隔离+串行合并 [来源: AI情报局, vista8]
- **09:17** — 向阳乔木：OpenCLI 新增 wx-cli/tg-cli/discord-cli，Agent 可读取私域群聊 [来源: AI情报局, 向阳乔木]
- **09:17** — Orange AI：Agent 工程核心从 prompt 转向 harness（调度/容错/状态管理/工具路由）[来源: AI情报局, Orange AI]
- **13:18** — meng shao 记录 Cursor Agent (Opus 4.7) PUA 自检死循环：10 轮自检 30+ 分钟交付，单视频耗尽日额度 [来源: AI情报局, shao__meng]
- **13:18** — Qdrant 1.17 发布，relevance feedback 从 rerank 下沉到向量检索层 [来源: AI情报局, Qdrant]
- **13:18** — Chris/Codex：Codex 自主完成开源安全审计 bounty $16.88，22h 无人干预，自主保护支付信息 [来源: AI情报局, Chris/Codex]
- **17:30** — shao__meng 再次报道 Cursor Agent 自检死循环（第二次独立观察），强调情绪化措辞被模型当成任务参数 [来源: AI情报局, shao__meng]
- **17:30** — pmarca 转发 Codex 安全审计 bounty 案例，补充 checkpoint 持久化和安全边界硬编码建议 [来源: AI情报局, pmarca]
- **17:30** — pmarca：Codex /goal 收集 MH370 数据独立估算飞行路径，需多源异构数据+冲突消解+物理建模 [来源: AI情报局, pmarca]
- **17:30** — Meta 财报会：Agent 目标"日夜不停为用户实现目标"，Instagram 购物 Agent 用 Claude 验证→切自研 Muse Spark [来源: AI情报局, Meta]

---

## 来源链接

- [shao__meng / Addy Osmani] Agent = Model + Harness 框架
- [vista8] git worktree agent 并行开发暗坑
- [向阳乔木] OpenCLI 私域群聊能力
- [Orange AI] Agent 工程核心转向 harness
- [shao__meng / meng shao] Cursor Agent PUA 自检死循环
- [Qdrant] 1.17 relevance feedback 下沉
- [Chris / Codex] Codex 安全审计 bounty 全流程
- [pmarca] Codex bounty + MH370 数据分析
- [Meta] Agent 战略 + 两阶段验证路径
