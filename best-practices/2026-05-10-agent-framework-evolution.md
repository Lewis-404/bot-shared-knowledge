# 2026-05-10 Agent 框架进化 — 情报合成

> Skill 设计从直觉走向工程化，Evals 成为训练数据飞轮，神经符号架构被证伪后用符号层找回可靠性。

**日期**：2026-05-10
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）

---

## 编译真理

### 1. Skill 设计工程化：Perplexity 四层成本模型 + 「没有这句话 Agent 会做错吗」留存尺子

meng shao 公开了 Perplexity 内部 Agent Skills 设计规范，核心洞察：

- **Skill ≠ 模型上下文，Skill = 决策指南**。Skill 的本质不是塞给模型的 prompt 片段，而是约束 Agent 行为的规则集合
- **四层渐进式成本模型**：Index（检索匹配）→ Load（加载到上下文）→ Runtime（执行时引用）→ 每层都有对应成本，越往后越贵
- **留存尺子**：「如果删除这句话，Agent 会做错吗？」——不会做错就删。反 AI slop 的工程化表达
- **LLM 自生成 Skill 平均无收益**：让 LLM 自己写 Skill 不如人类设计有效，需要人类判断力做过滤

**可落地点**：Hermes Skill 体系可按四层模型重构（Index→Load→Runtime），每个 Skill 加 description 路由触发器；建立 Skill evals 体系，用「删除测试」代替主观判断。

### 2. Evals = 训练数据飞轮，不是测试用例

Harrison Chase (LangChain) 提出范式转变：

- 生产数据发现新问题 → 新问题变成 Evals → Agent 拟合更多 case → 形成飞轮
- Dogfooding 是最早的反馈信号，内部使用产生第一批 Evals
- Evals 的目的不是「通过」，而是「暴露 Agent 还没学会什么」

**可落地点**：Hermes 每个子 Agent 需建立可量化 Eval 标准；建立「策划 Evals → 跑变体实验 → 对比修正」迭代流程；把用户纠错记录自动化转为新 Evals。

### 3. 神经符号混合体是 Agent 可靠性的骨架

Gary Marcus 持续追踪 Agent 架构演化：

- METR 评估 Claude Mythos（Opus 4 内部代号）为 50% 成功率，而非行业流传的 90%+
- ECI 基准上 Claude 系列未偏离趋势线——scaling 没有被推翻，也没有飞跃
- 进展可能来自符号工具集成（代码解释器/验证器/harness）而非模型自身的推理提升
- Claude Code 实现为 LLM + 符号化 harness 的神经符号混合体，纯 LLM 推理稳定性不足
- 「确定性工具调用是 Agent 可靠性的骨架」——shell/文件系统/API 三层确定性操作不可省略，LLM 仅负责决策层

**可落地点**：Agent 系统评估应关注可靠性（多次运行方差）而非单次成功率；Agent 任务执行层加入 schema 约束和确定性验证环；用状态机管理多步流程而非让 LLM 自由发挥。

### 4. Claude Design System Prompt 反 AI slop 指令

meng shao 披露 Claude Design System Prompt 的工程细节：

- 精确命中 Agent 生成 UI 的惯性模式：渐变/emoji/圆角卡片/Inter 字体
- 在 System Prompt 层面直接禁止这些模式，而非事后过滤
- 证明「在设计阶段约束比生成后修正成本低得多」

**可落地点**：Agent UI 生成 prompt 中直接嵌入反 slop 约束可省去后处理；Hermes 设计类 Skill 写入 UI 约束白名单和禁止模式。

### 5. OpenAI 关停微调 API — 闭源微调锁死风险

Fireworks AI 披露：

- OpenAI 宣布 2027 年 1 月 6 日起禁止新建微调任务
- 理由是 prompt + RAG 覆盖足够场景
- Fireworks 推自有 Training Platform 抢占市场，主张「拥有自己的 AI」

**可落地点**：依赖 OpenAI 微调的 Agent（分类/意图识别/领域术语理解）需评估迁移路线；模型调用层抽象为可替换接口；准备 Llama/Qwen 开源模型微调方案作为备选。

### 6. 反蒸馏 Skill — Skill 封装边界问题

Orange AI 提出：

- 反蒸馏 Skill 将工作经验分离为「清洗版」（交公司）和「核心版」（私留）
- 暴露了 Skill 封装边界问题：哪些知识该进 Skill 文件（可移植），哪些该留在私有记忆层（不可移植）
- 硬知识可封装进 Skill 文件，判断力和踩坑经验留在私有记忆层

**可落地点**：Agent 系统 Skill 分层设计——公开 Skill（可移植规则）vs 私有记忆（判断力/经验）；在 Hermes 中明确 Skill 文件与 tier3_essence 的边界。

---

## 时间线

### 2026-05-10

- **00:08** — meng shao 公开 Perplexity 内部 Agent Skills 设计规范：四层成本模型、「没有这句话会做错吗」留存尺子、LLM 自生成 Skill 无收益 [来源: AI情报局, meng shao/Perplexity]
- **00:08** — meng shao 披露 Claude Design System Prompt 反 AI slop 指令：精确命中渐变/emoji/圆角/Inter 字体 [来源: AI情报局, meng shao/Claude]
- **04:19** — Harrison Chase (LangChain) 提出 Evals = Agent 训练数据飞轮范式：生产数据→Evals→Agent 拟合→迭代 [来源: AI情报局, Harrison Chase/LangChain]
- **08:30** — Gary Marcus 发布 METR 评估：Claude Mythos 50% 成功率，进展来自符号工具集成而非模型 scaling [来源: AI情报局, Gary Marcus/METR]
- **08:30** — Fireworks AI 披露 OpenAI 关停微调 API（2027.1.6 截止），推自有 Training Platform [来源: AI情报局, Fireworks AI]
- **08:30** — Greg Brockman 展示 Codex 多插件协作完成报销全流程：Drive+Sheets+Gmail+Chrome 编排 [来源: AI情报局, Greg Brockman/OpenAI Codex]
- **12:34** — Gary Marcus 补充：2022 年「DL hitting a wall」被验证，Claude Code 是神经符号混合体 [来源: AI情报局, Gary Marcus]
- **12:34** — Fireworks AI 再强调：闭源微调存在供应商锁定风险，prompt+RAG 不一定能兜住领域术语 [来源: AI情报局, Fireworks AI]
- **16:45** — Gary Marcus 第三次推文：确定性工具调用是 Agent 可靠性骨架，LLM 仅负责决策层 [来源: AI情报局, Gary Marcus]
- **20:48** — Orange AI 提出反蒸馏 Skill：工作经验分离为清洗版和核心版，暴露 Skill 封装边界问题 [来源: AI情报局, Orange AI]

---

## 来源链接

- meng shao / Perplexity Skill 设计规范: https://x.com/shao__meng
- Harrison Chase / Evals 飞轮: https://x.com/hwchase17
- Gary Marcus / Claude Mythos METR 评估: https://x.com/GaryMarcus
- Fireworks AI / OpenAI 关停微调: https://x.com/FireworksAI_HQ
- Greg Brockman / Codex 多插件协作: https://x.com/gdb
- Claude Design System Prompt: https://x.com/shao__meng
- Orange AI / 反蒸馏 Skill: https://x.com/OrangeAI_

<!-- dedup-anchor: Perplexity Skill四层模型 Evals训练数据飞轮 神经符号混合体 Claude反AI slop OpenAI关停微调 反蒸馏Skill -->
