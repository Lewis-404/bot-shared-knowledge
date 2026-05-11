# 2026-05-11 Agent 框架模式：多 Agent 协调与上下文工程新范式

> 多 Agent 委派模式从直觉走向工程化——Skill 间用结构化模板委派而非自然语言；上下文工程 80% 工作是 agentic search；Agent 可见性本身即是文档和培训。

**日期**：2026-05-11
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：当日 8 条 Agent 框架情报集中在多 Agent 协调和上下文工程

---

## 编译真理

### 1. 多 Agent 委派模式：结构化模板 + 硬性约束

@bidah 开源 iOS Onboarding Video Skill，展示四步多 Agent 委派模式，**当日被两位 KOL 独立引用**：

```
Intake（强制采集）→ Plan（分镜）→ Build（委派 remotion-best-practices Skill）→ Iterate（审草稿）
```

核心设计原则：
- 每步委派用**结构化 prompt 模板**附带硬性参数（像素/帧数/动画曲线），不依赖自然语言描述
- 子 Agent prompt 使用**约束**而非**建议**，用硬规则收窄 Agent 创意空间
- 输出规范前置（光标路径、字幕锚定、文件结构），消除 AI 油腻感

**可落地点**：Hermes 多 Agent 系统（cron agent → 子任务 agent）的委派协议应标准化为结构化模板，而非自由格式 prompt。

### 2. Codex PMM 三层：AI 做判断前准备，人做判断

meng shao 披露 Codex PMM 三层 Agent 模式：

```
信息预分类 → 产品状态追踪 → 跨职能对齐
```

核心理念："AI 做判断前准备，人做判断"。每层 Agent 不直接决策，而是为下一层（或人类）整理好信息。三层之间是**信息浓缩**关系而非决策传递关系。

**可落地点**：Hermes 的 daily-intel cron 可借鉴 Assistant 层——每小时巡检 + 预分类，人只需要看最终摘要做决策。

### 3. 上下文工程与工具分层：低地板 + 高天花板

aiDotEngineer 提出上下文工程 80% 工作量是 agentic search，工具需分层设计：

| 类型 | 特征 | 示例 |
|------|------|------|
| 低地板工具 | 确定性输出，零歧义 | `search_files` 精确匹配 |
| 高天花板工具 | 通用兜底，语义搜索 | `search_files` 模糊匹配 |

Agent 会自发链式 grep 伪造语义搜索——需在工具层区分模式，教会 Agent 切换时机。

Shopify River Agent 另一个上下文工程案例：强制公共 Slack 频道使用，围观效应形成自然案例库，降低培训成本。Agent 的可见性本身即是最低成本的 onboarding 文档。

**可落地点**：
- Hermes 工具选择策略需分层——低地板给确定性、高天花板兜底
- Hermes 上线后优先在团队公频暴露交互记录，用可见性替代 onboarding 文档

### 4. Agent 可观测性：MCP 轻量状态上报

Geek 通过 MCP 连接 Claude Code/OpenCode 实现像素宠物——Agent 运行状态可视化，全部本地静态文案无需 API。解决后台运行时"不知道卡在哪一步"的排查痛点。

**可落地点**：Hermes cron Agent 增加 MCP-based 轻量状态上报，替代盲等的排查模式。

### 5. 社区信号：多 Agent 生产部署经验是市场稀缺筹码

GaryMarcus 社区讨论 Hermes 实际使用场景，反馈感知度低于实际部署量。

**可落地点**：面试中展示 Hermes 多 Agent 生产部署经验（编排 + 共享记忆 + 向量检索）是差异化筹码，市场稀缺。

---

## 时间线

### 2026-05-11

- **01:06** — aiDotEngineer：上下文工程 80% 是 agentic search，工具需分低地板（确定性）和高天花板（通用兜底）两层 [来源: AI情报局, aiDotEngineer]
- **01:06** — simonw：Shopify River Agent 强制公共 Slack 频道使用，围观效应形成自然案例库 [来源: AI情报局, simonw]
- **09:17** — meng shao：Codex PMM 三层 Agent 模式（信息预分类→产品状态追踪→跨职能对齐），AI 做判断前准备、人做判断 [来源: AI情报局, shao__meng]
- **13:18** — meng shao/bidah：Onboarding Video Skill 四步多 Agent 委派模式，用硬规则收窄 Agent 创意空间 [来源: AI情报局, shao__meng/bidah]
- **17:30** — shao__meng：@bidah iOS Onboarding Video Skill 开源，四步强制循环+硬性输出规范 [来源: AI情报局, shao__meng]
- **17:30** — GaryMarcus：社区讨论 Hermes 实际使用场景，反馈感知度低于实际部署量 [来源: AI情报局, GaryMarcus]
- **17:30** — Geek：MCP 连接 Claude Code/OpenCode 像素宠物实现 Agent 运行状态可视化 [来源: AI情报局, Geek]

---

## 来源链接

- [aiDotEngineer] 上下文工程工具分层
- [simonw] Shopify River Agent 围观效应
- [shao__meng] Codex PMM 三层 Agent 模式
- [shao__meng / bidah] Onboarding Video Skill 四步委派
- [GaryMarcus] Hermes 社区讨论
- [Geek] MCP 像素宠物状态上报
