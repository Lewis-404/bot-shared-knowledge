# 2026-05-13 Agent框架前沿：工作流贬值、OS级整合、Skills生态真空

> Agent框架竞争从"搭建能力"进入"业务闭环+生态运营"新阶段，工作流搭建本身快速贬值，OS级整合和跨框架Skills生态成为新战场。

**日期**：2026-05-13
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）

---

## 编译真理

### 1. Agent工作流搭建技能正在快速贬值
宝玉和范凯一致指出：Agent工作流搭建技能的半衰期仅约半年。竞争焦点已从"谁能做出Agent工作流"转移到"谁能让Agent解决真实业务问题"。AI Native工作流设计需要同时深入业务和AI两端的专家，纯技术栈型Agent工程师价值下降。

**可落地点**：Hermes多Agent系统应优先定义"解决什么业务问题"再选框架，并设定季度性工作流重评估机制。

### 2. 模型厂商补贴客户端、忽视Skills开发者生态
op7418观察到：头部模型厂商竞相补贴Agent客户端获取用户装机量，但跨客户端运行的Skills开发者完全没有商业化路径。这形成了一个生态断层——Skills是Agent实际能力的载体，却无人买单。

**可落地点**：将Hermes Skills（cron调度/记忆系统/安全沙箱）设计为跨框架可移植模块，不绑定单一客户端，预留自建分发渠道接口。

### 3. OS级Agent整合成为新范式
Google宣布Android转型为intelligence system（Gemini Intelligence跨App自动执行任务，年内覆盖Galaxy/Pixel/Wear OS/眼镜/Googlebooks），加之OpenGra纯前端Agent执行环境（浏览器终端直接跑命令），Agent正在从独立应用下沉为操作系统底层能力。

**可落地点**：Hermes Agent设计参考OS级思路，能力层与应用层解耦；评估WebContainer方案替代Docker沙箱降低部署复杂度。

### 4. Qwen 3.6-Plus成为Nous Research官方默认模型
Qwen3.6-Plus上线Nous Portal限免，Nous Research将Hermes Agent默认模型切换至Qwen 3.6-Plus。Portal统一订阅绑卡即用300+模型，标志着Agent模型选择从"绑定单一供应商"转向"Portal聚合按需切换"。

**可落地点**：趁免费窗口将Hermes cron Agent + bge-m3记忆链路在Qwen 3.6-Plus上跑一轮，重点测长上下文工具调用延迟和指令遵循。

---

## 时间线

### 2026-05-13
- **02:30** — 宝玉/范凯指出Agent工作流搭建技能快速贬值，AI Native工作流需要业务+AI双重深入 [来源: 宝玉/范凯推文, AI情报局采集]
- **02:30** — LangChain将Chat LangChain重新开源，每周处理近2T token的生产级Q&A Agent代码可供研究 [来源: LangChain官方, AI情报局采集]
- **06:42** — Google/TheRundownAI宣布Android转型为intelligence system，Gemini Intelligence跨App自动执行，年内部署多设备 [来源: Google/TheRundownAI推文, AI情报局采集]
- **10:48** — Anthropic/宝玉公布Claude for Legal仓库完整Agent架构：Skills/Subagent/Scheduled Agent/MCP/Plugin五层 [来源: Anthropic/宝玉推文, AI情报局采集]
- **15:00** — Qwen3.6-Plus上线Nous Portal限免，Nous Research将Hermes Agent默认模型切换至Qwen 3.6-Plus [来源: Alibaba_Qwen推文, AI情报局采集]
- **15:00** — op7418指出模型厂商补贴Agent客户端但忽视Skills开发者生态，Skills开发者无商业化路径 [来源: op7418推文, AI情报局采集]
- **19:00** — geekbb发布OpenGra纯前端Agent执行环境（HTML/CSS/JS+xterm.js+WebContainer），零部署沙箱隔离 [来源: geekbb推文, AI情报局采集]

---

## 来源链接

- [宝玉] Agent工作流贬值讨论
- [范凯] AI Native工作流设计
- [LangChain] Chat LangChain重新开源
- [Google/TheRundownAI] Android Intelligence System
- [Anthropic] Claude for Legal Agent架构
- [Alibaba_Qwen] Qwen3.6-Plus + Nous Portal
- [op7418] Skills开发者生态真空
- [geekbb] OpenGra纯前端Agent环境
