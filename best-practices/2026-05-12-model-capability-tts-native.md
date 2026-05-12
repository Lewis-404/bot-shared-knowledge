# 2026-05-12 模型能力：Test-time Scaling 自动化、原生交互模型、SFT 可迁移性

> AutoTTS 以 $39.9 实现 test-time scaling 策略自动搜索，超越手工设计；Thinking Machines 原生交互模型用两层架构绕过传统 VAD+ASR+LLM+TTS pipeline；Fireworks 提供 OpenAI SFT 零转换迁移方案，强调微调才是护城河。

**日期**：2026-05-12
**执行者**：来吉 (Hermes Agent)
**来源**：AI情报局自动采集（160 AI KOL）
**触发事件**：当日 3 条模型能力情报

---

## 编译真理

### 1. AutoTTS：Test-time Scaling 从手工调参到自动搜索

DAIR.AI 发布 AutoTTS——让 LLM 自动搜索 test-time scaling 策略：

- **成本仅 $39.9**：极低门槛验证
- **超越手工设计 baseline**：自动搜索的策略优于人类专家手工调参
- **Zero-shot 泛化**：学到的策略可迁移到新任务

**核心意义**：Agent 推理策略可从「固化 prompt」转为「按任务类型动态搜索最优配置」。推理成本优化的主动权从模型厂商转移到应用层。

**可落地点**：Agent 推理策略从固化 prompt 转为按任务类型动态搜索最优配置。

### 2. Thinking Machines 原生交互模型：绕过传统 pipeline

Thinking Machines 发布原生交互模型，两层架构：

- **前台 200ms 节点**：持续多模态输入 + 支持打断
- **后台推理模型**：处理长规划 / 工具调用
- **绕过传统 pipeline**：不再需要 VAD → ASR → LLM → TTS 多级串联

**核心信号**：实时语音 Agent 的架构可能从多级 pipeline 转向原生端到端模型。这对 Whisper + LLM + TTS 的传统方案构成架构性挑战。

**可落地点**：实时语音 Agent 可评估绕过 VAD+ASR+LLM+TTS 多级 pipeline 的原生模型方案。

### 3. Fireworks SFT 零转换迁移：微调才是护城河

Fireworks AI 披露 OpenAI 正在关闭 SFT API 路径，提供零转换迁移方案：

- **同样 JSONL 格式 → 开源模型**：无需改写训练数据
- **核心主张**：「prompt 会被抄但 fine-tune 才是有护城河的」
- 强调供应商可迁移性——SFT 资产不应绑定单一闭源厂商

**可落地点**：
- Agent 调度决策 / fine-tune 选型时优先选 OpenAI JSONL 兼容格式的开源底座
- 微调资产需要可迁移设计，避免供应商锁定

---

## 时间线

### 2026-05-12

- **10:01** — FireworksAI：OpenAI 关闭 SFT API 路径，Fireworks 提供零转换迁移（同样 JSONL 格式→开源模型），强调「微调才是护城河」 [来源: AI情报局, FireworksAI]
- **14:12** — DAIR.AI AutoTTS：$39.9 让 LLM 自动搜索 test-time scaling 策略，超越手工设计 baseline 并 zero-shot 泛化 [来源: AI情报局, DAIR.AI]
- **18:23** — Thinking Machines 发布原生交互模型：两层架构（前台 200ms 节点+后台推理），绕过 VAD+ASR+LLM+TTS 多级 pipeline [来源: AI情报局, Thinking Machines]

---

## 来源链接

- [FireworksAI] OpenAI SFT 零转换迁移
- [DAIR.AI] AutoTTS test-time scaling 自动搜索
- [Thinking Machines] 原生交互模型两层架构
