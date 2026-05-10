# 2026-05-10 模型能力边界与成本前沿 — 情报合成

> Claude Mythos 50% 成功率戳破 90%+ 叙事，AI 擅长/不擅长的判断标准日益清晰，Pareto Code 路由实现同质量下成本自动寻优，DS4 C 引擎拉低本地部署门槛。

**日期**：2026-05-10
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）

---

## 编译真理

### 1. Claude Mythos 真实成功率 50%，90%+ 是行业叙事泡沫

Gary Marcus 引用 METR 评估结果：

- Claude Mythos（Opus 4 内部代号）真实成功率为 50%，非行业流传的 90%+
- ECI 基准上 Claude 系列未偏离趋势线——scaling 仍在继续但没有跃进
- **核心推论**：进展来自符号工具集成（代码解释器/验证器/harness）而非模型自身推理的飞跃
- **评估教训**：Agent 系统应评估可靠性（多次运行方差）而非单次 peak 成功率

**可落地点**：Agent 评估体系必须引入多次运行稳定性指标；对外的「成功率」声明需附带评估方法和方差数据。

### 2. AI 擅长/不擅长的判断标准：你能验证结果吗？

Aadit Sheth 提出实用判断框架：

| 擅长 | 不擅长 |
|------|--------|
| 可验证代码 | 品味判断 |
| 初稿/草稿 | 情感决策 |
| 脏活/体力活 | 不可验证的高风险 |
| 陌生代码库探索 | 需要人类直觉的抉择 |

**核心判断标准**：「你能验证 AI 的输出吗？」——能 → 放心用；不能 → 需要人类把关。

**可落地点**：Agent 输出校验机制内建到每个 Agent 流水线；不可验证输出不直接送达用户；每个 Agent 定义明确的「可验证性」边界。

### 3. Pareto Code 模型路由：同质量自动寻优，成本数月降 10-100 倍

OpenRouter 发布 Pareto Code 路由器：

- 按代码质量门槛自动选当时最便宜的模型
- 同质量档位成本每几个月降 10-100 倍
- 无需人工调参——设定质量门槛后自动路由
- 推文中被 3 个不同 KOL 独立引用，反映行业对「智能降本」的强烈需求

**可落地点**：多 Agent 系统按 task type 设定 baseline 动态路由模型；代码生成走 Pareto Code 压成本，关键推理锁死最强模型；实现自动降本无需人肉调参。

### 4. DS4 C 推理引擎：单模型极致优化拉低本地部署门槛

Yangyi/antirez 发布 DS4：

- 用 C 语言为 DeepSeek V4 Flash 做 2-bit 本地推理引擎
- 单模型专用引擎替代通用方案
- 显著降低边缘设备的延迟和内存约束

**可落地点**：本地 Agent 部署选型时关注单模型专用推理引擎替代通用方案；DeepSeek 生态的 C 级推理优化值得跟踪。

### 5. 字节推理成本 80 亿超营收 2.3 倍 — 模型经济学红线

Orange AI 披露字节内部数据：

- 2025 年推理成本 80 亿，为 AI 营收增量的 2.3 倍
- CFO 称现金流撑不到 2027
- 从「什么都试」转向深耕豆包 + AI 硬件
- **核心教训**：模型推理成本是 Agent 产品化的隐形天花板，ROI 算不过账一切免谈

**可落地点**：Agent 产品设计中推理成本必须是第一性约束而非事后优化；模型路由层（如 Pareto Code）的降本价值被低估——可能决定产品生死。

---

## 时间线

### 2026-05-10

- **00:08** — Gary Marcus 引用 METR 评估：Claude Mythos 50% 成功率，进展来自符号工具集成 [来源: AI情报局, Gary Marcus/METR]
- **04:19** — Aadit Sheth 提出 AI 擅长/不擅长判断标准：核心是「你能验证结果吗」 [来源: AI情报局, Aadit Sheth]
- **08:30** — OpenRouter 发布 Pareto Code 路由器：同质量自动选最便宜模型，成本数月降 10-100 倍 [来源: AI情报局, OpenRouter]
- **12:34** — OpenRouter Pareto Code 二次传播：行业对智能降本的强烈需求 [来源: AI情报局, OpenRouter]
- **12:34** — Orange AI 披露字节推理成本 80 亿超营收 2.3 倍，战略收缩至豆包+硬件 [来源: AI情报局, Orange AI/字节跳动]
- **16:45** — OpenRouter Pareto Code 三次传播：强调按任务类型分路由策略 [来源: AI情报局, OpenRouter]
- **20:48** — Yangyi/antirez 发布 DS4：C 语言为 DeepSeek V4 Flash 做 2-bit 本地推理引擎 [来源: AI情报局, Yangyi/antirez]

---

## 来源链接

- Gary Marcus / Claude Mythos METR: https://x.com/GaryMarcus
- METR Evaluation: https://metr.org
- Aadit Sheth / AI 擅长判断标准: https://x.com/aaditsh
- OpenRouter / Pareto Code: https://openrouter.ai
- Orange AI / 字节推理成本: https://x.com/OrangeAI_
- Yangyi/antirez / DS4 C 推理引擎: https://x.com/yangyi_ai
