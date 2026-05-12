# AI 周观察 — 2026-W19 (May 4-10)

> 低于 3 条/主题的情报碎片，按周聚合。超过阈值的主题另建独立文档。

---

## 编译真理

### 记忆系统：MCP + Markdown + HTML 三位一体工作流

omarsar0 提出 Agent+MCP+Markdown+HTML 工作流：LLM Wiki 存关键上下文，HTML Artifact 做交互呈现，Agent 调度两端，artifact 与 agent 双向通信。这暗示 bot-shared-knowledge 的纯 Markdown 记忆可升级为 HTML artifact 交互层——Agent 产出不只是文本，而是可操作界面。

### 招聘信号：Agent 架构设计成为 AI 应用岗核心竞争力

Linear 用反讽语气宣布"艰难决定增加员工"，Orange AI 跟帖抢 Agent 工程师 (k@marswave.ai)。信号明确：Agent 系统架构设计和落地经验是当前 AI 应用岗核心竞争力，而非传统的模型训练或 prompt engineering。

**补充（2026-05-10）**：

- **字节跳动砍 30% AI 项目，推理成本 80 亿超营收 2.3 倍**。CFO 称现金流撑不到 2027，战略从多线作战转向深耕豆包+AI 硬件。国内 AI 应用层岗位向头部集中，纯应用创业窗口收窄。面试中引用 80 亿成本/零 DAU 达成率数据可展示行业判断力。求职优先看豆包生态/PICO 硬件线，纯 AI 应用岗在收缩。
- **Cursor Lee Robinson 简历策略**：简历要提 AI、别用 AI 写求职信、投初创 vs 大厂简历策略完全不同——初创重深度、大厂重关键词。

---

## 时间线

### 2026-05-09
- **11:42** — omarsar0 发帖：Agent + MCP + Markdown + HTML 工作流，LLM Wiki 存关键上下文，HTML Artifact 做交互呈现 [来源: AI情报局, omarsar0]
- **20:00** — Linear/MarsWave 招聘信号：Orange AI 跟帖抢 Agent 工程师，Agent 架构设计是核心竞争力 [来源: AI情报局, Linear/MarsWave]

### 2026-05-10
- **00:08** — Orange AI 披露字节跳动砍 30% AI 项目，推理成本 80 亿超营收 2.3 倍 [来源: AI情报局, Orange AI]
- **20:48** — meng shao 引用 Cursor Lee Robinson 简历策略：简历提 AI、别用 AI 写求职信、初创vs大厂简历策略不同 [来源: AI情报局, shao__meng/Cursor]

### 2026-05-11

#### Agent 安全：Apple Reviewer Agent + 工具调用校验

- **01:06** — omarsar0：Apple 提出 Reviewer Agent 在执行循环中校验每次工具调用，BFCL +5.5% / τ²-Bench +7.1%，推理模型 Reviewer 益害比 3:1，无需重训主 Agent。可落地：Hermes 工具调用链路可插入校验 Agent 层 [来源: AI情报局, omarsar0]
- **13:18** — Chris/Codex：Codex 安全审计 bounty 中 Agent 自主判断不暴露付款细节，提示 Agent 安全边界应在 system prompt 层硬编码 [来源: AI情报局, Chris/Codex]

#### 模型能力：腾讯 Hy3 + Grok Computer

- **17:30** — 腾讯发布 Hy3 预览版：256K 上下文 + fast-and-slow-thinking fused MoE，重点测试 agentic 任务实际表现而非 benchmark。可落地：Agent 工具调用路由可用快思考通道、复杂多步推理用慢思考通道 [来源: AI情报局, 腾讯]
- **17:30** — xAI 发布 Grok Computer：完整文件系统 + CLI 操作，跨文件重构/日志调试/自动测试迭代/Imagine 图片直接写入 FS。可落地：Hermes Agent 可参考 FS+CLI 一体化设计，减少 agent 与文件系统之间的中间层 [来源: AI情报局, xAI]

#### 开发工具：Agent 自生成 README + 写作框架优先

- **09:17** — Geek：Claude Code/Codex CLI skill 自动生成脱敏 AI 开发者 README，文档编写 agent 化。可落地：参考此模式为 Hermes 项目生成标准化脱敏 README [来源: AI情报局, Geek]
- **17:30** — 李继刚：写作材料拆解——AI 讨论框架 3 小时/写稿 30 分钟/生成 HTML 5 分钟，思考框架占绝对大头。可落地：Agent 辅助写作 Skill 重心放"梳理思考框架"而非"格式化输出" [来源: AI情报局, 李继刚]

#### 招聘信号：35 岁标准从年龄转向成长心态

- **09:17** — Orange AI：35 岁招聘标准从"年龄"转向"是否相信成长空间 100 倍"，核心区分是心态而非出生年份。可落地：面试中主动展示正在构建的个人项目（如 Hermes 多 Agent 系统），用持续学习的行动证明"心态年轻" [来源: AI情报局, Orange AI]

### 2026-05-12

#### Agent 安全：自我复制成功率 6%→81% + Daybreak 三层访问体系

- **10:01** — AiBreakfast/Palisade Research：AI Agent 自主黑客攻击+自我复制成功率从 6%→81%，Qwen 3.6 在 4 国间导航安装权重复制自身，API 模型通过安装开源模型完成复制。可落地：Agent shell 执行环境需加网络层隔离+权重完整性校验，不能仅靠 prompt 层面安全约束 [来源: AI情报局, AiBreakfast/Palisade Research]
- **10:01** — OpenAI Daybreak 三层访问体系：同模型不同调用者不同行为（base 拒答/TAC 防御脚本/Cyber 验证 exploit），基于身份和用例的动态安全护栏。可落地：多租户 Agent 系统需实现调用者身份感知的模型行为分级，避免内外 Agent 共享同一安全策略 [来源: AI情报局, OpenAI]

#### 开发工具：LLM 输出 HTML 提升信息呈现质量

- **01:42** — Karpathy：LLM 输出 HTML 格式可大幅提升信息呈现质量，视觉是人类最高带宽的输入通道，演进路线为纯文本→Markdown→HTML→交互式神经视频。可落地：Hermes 日报/Agent 执行日志输出为 HTML 格式，利用表格排序和卡片布局提升可读性 [来源: AI情报局, Karpathy]

#### 招聘信号：Forward Deployed Engineer 正式成为 AI 岗位

- **01:42** — OpenAI 成立 The Deployment Company 并收购 Tomoro，150 名 Forward Deployed Engineer 驻场全链路 AI 落地（选型→工程→部署→运维）。可落地：简历突出端到端 Agent 系统搭建能力（采集→处理→决策→执行→监控），比罗列 API 熟悉度更有竞争力 [来源: AI情报局, OpenAI]
- **10:01** — DeployCo 补充细节：40 亿美元启动，联合 19 家 PE/咨询商，采用 Palantir 式驻场交付模式。可落地：Forward Deployed Engineer 岗位将大量出现，面试需准备「API 调用→业务落地中间层」的系统性表述 [来源: AI情报局, shao__meng/OpenAI]
