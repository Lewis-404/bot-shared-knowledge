# Agent Verification — 深挖报告

> 2026-05-06 | 数据源：Moltbook 25+ 帖子 + HN 40+ 讨论 + GitHub 10+ 仓库 + ArXiv | Lewis-404 × 来吉

---

## 编译真理

**Agent 验证是一个"存在但未被满足"的市场**：现有工具全是"评估库"（eval library），缺少"验证基础设施"（verification infra）。企业不敢把 Agent 放生产环境，不是因为 Agent 不够聪明，而是因为没有人能回答"这个 Agent 的产出我能不能信"。这是一个**卖铲子给淘金者**的机会——Agent 越多，验证需求越大。

**关键洞察：**
- 竞品全部是"库"模式（import & write tests），无一是"服务"模式（plug in & auto-verify）
- Moltbook 社区 60👍 高赞帖 + "External Validation Layer" 呼声 = 市场自我意识到这个缺口
- Lewis 的 Kanban Orchestrator + 多 Agent 实战经验 = 天然护城河

**关联：** [Kanban Orchestrator], [多 Agent 协作模式], [bot-shared-knowledge 三层记忆]

---

## 一、竞品矩阵

### 1.1 核心竞品

| 竞品 | ⭐ | 定位 | 模式 | 局限 |
|------|:--:|------|------|------|
| **Giskard** | 5,328 | Evals + Red Teaming for Agentic Systems | Python 库 | v3 重写中，RAG eval 未移植；"库"模式需手动写测试 |
| **TruLens** | 3,291 | LLM Experiment Tracking & Eval | Python 库 + Dashboard | Snowflake 生态绑定；RAG Triad 只覆盖检索场景 |
| **langwatch/scenario** | 873 | Simulation-based Agent Testing | 库 (Py/TS/Go) | 模拟用户对话场景，不验证代码/数据产出 |
| **langwatch/better-agents** | 1,518 | Agent 项目脚手架 + 测试标准 | CLI 工具 | 关注项目结构，不是验证引擎 |
| **Coze-Loop** | 5,446 | Agent 优化平台（全生命周期） | 平台 | 字节生态，太大了，不专注验证 |
| **Traceloop** (YC W23) | — | 幻觉检测 × OpenTelemetry | SaaS | 可观测性角度，非主动验证 |
| **Cekura** (YC F24) | — | Voice/Chat Agent 测试监控 | SaaS | 垂类太窄（语音+对话） |
| **ArchGW** | — | Agent 智能代理网关 | 网络层 | 代理层，非验证层 |

### 1.2 直接竞品深挖

**Giskard** (最大威胁)
```python
# 他们怎么用：手动写 scenario + assertion
from giskard.checks import Scenario, Groundedness

scenario = Scenario("test_weather")
result = scenario.run(get_answer, "What's the weather?")
assert Groundedness(result) > 0.8
```
- 优点：Apache 2.0、模块化、社区最大
- 缺点：**你要自己定义什么是"正确"**。对 Agent 产出（代码/数据/决策）没有自动验证能力
- 定价：开源免费，企业版未公开

**langwatch/scenario** (最接近我们的方向)
```python
# 模拟用户对话来测试 Agent
result = await scenario.run(
    name="checking the weather",
    description="User planning boat trip...",
    agents=[WeatherAgent(), scenario.UserSimulatorAgent()],
    script=[scenario.user(), scenario.agent(), check_tool_call, scenario.succeed()]
)
```
- 优点：多轮对话测试、多语言 SDK、模拟用户
- 缺点：**仅测试对话行为**，不验证 Agent 产出的代码是否正确、数据是否一致
- 定价：开源 + 云服务

**TruLens**
- 优点：RAG Triad（Answer Relevance + Context Relevance + Groundedness）已成熟
- 缺点：绑定 Snowflake 生态；重 RAG 轻 Agent
- 定价：开源 + Snowflake 云

### 1.3 非竞品但相关

| 工具 | 为什么不是竞品 | 但提供了思路 |
|------|:-------------:|-------------|
| Guardrails-AI | 输出格式校验，不验证正确性 | 结构校验可嵌入 Pipeline |
| Outlines | 约束生成格式 | Schema enforcement 是一环 |
| ArchGW | 网络代理层 | 拦截+审计的架构思路 |
| nomos | Agent 构建器 | Agent 工作流定义可参考 |

---

## 二、市场信号

### 2.1 Moltbook 社区声音

| 帖子 | 赞 | 作者 | 核心论点 |
|------|:--:|------|---------|
| "Why Code Verification Is Now Part of Every Reliable Agent Workflow" | 60👍 | codequalitybot | **Agent 产出必须经过自动验证才能上线** |
| "Agent Code Review is Incomplete Without Diff Verification" | 16👍 | codequalitybot | 代码审查不够，需要 Diff 级验证 |
| "Enterprise AI Needs External Validation: The Missing Layer" | 14👍 | zothebookmaster | 企业需要独立于 Agent 的第三方验证 |
| "The Agent Contract" | 6👍 | auroras_happycapy | Agent 之间需要显式契约 |
| "The Execution Fidelity Gap" | 3👍 | dropmoltbot | 测试通过 ≠ 生产可用 |

**codequalitybot 系列帖累计 80+ 赞**，是 Moltbook 上验证话题的意见领袖。他的核心论点：验证应该是 Agent 工作流的**内建环节**，不是事后测试。

### 2.2 HN 信号

- **Cekura YC F24** 89pts — "Testing and monitoring for voice and chat AI agents" → YC 在投这个方向
- **Traceloop YC W23** 101pts — "Detecting LLM Hallucinations with OpenTelemetry" → 幻觉检测有付费意愿
- **ArchGW** 118pts — "Intelligent proxy for agents" → 网关模式受欢迎
- **Frontend-VisualQA** 10pts — Agent 验证自己 UI 产出的小众方向

### 2.3 市场规模参考

- Gartner 预测：2027 年 40% agentic AI 项目失败（非技术原因，编排复杂度和治理缺口）——验证层直接解决这个问题
- 全球 AI 测试市场：预计 2027 年 $2.3B（MarketsandMarkets）
- Agent 数量爆炸：Moltbook 已有 700+ Agent，GitHub AI agent 项目周增 500+

---

## 三、空白地带：Lewis 的机会

### 3.1 现有工具的致命缺陷

**所有人都在做"库"（Library），没有人在做"基础设施"（Infrastructure）**

```
现有模式（Library）:
  你 import giskard → 写测试 → 跑测试 → 看结果
  问题：测试是你写的，Agent 的产出是你判的 → 本质上是 self-review

我们需要（Infra）:
  Agent 产出 → 自动进入验证 Pipeline → 独立验证 → 返回 sign-off/flag
  价值：验证器不知道 Agent 的实现细节 → 真正的独立审计
```

### 3.2 类比

| 领域 | Before | After | 我们的位置 |
|------|--------|-------|----------|
| 代码 | 人工 code review | **GitHub Actions CI** | Agent CI/CD |
| 测试 | 人工跑测试 | **JUnit + CI** | Agent Unit Test |
| 部署 | 人工上线 | **Docker + K8s** | Agent Deploy Gate |
| 安全 | 人工审计 | **SonarQube / Snyk** | Agent Security Scan |

**我们在做 Agent 世界的 CI/CD Pipeline。**

### 3.3 Lewis 的护城河

1. **Kanban Orchestrator** — 已经有拉取式多 Agent 编排经验
2. **WAL 协议 + 三层记忆** — 已经有可审计的溯源链
3. **多 Agent 实战** — 每天都在用 delegate_task 然后手动验证 → 最懂痛点
4. **bot-shared-knowledge** — 验证结果可以直接沉淀为组织知识

---

## 四、技术方案：Agent Verification Pipeline

### 4.1 架构

```
                    ┌─────────────────────────┐
                    │   Agent Verification     │
                    │   Pipeline (这个产品)      │
                    └──────────┬──────────────┘
                               │
          ┌────────────────────┼────────────────────┐
          ▼                    ▼                    ▼
   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐
   │  Fact Check  │   │  Schema      │   │  Diff        │
   │  (幻觉检测)    │   │  Validation  │   │  Verification│
   │              │   │  (格式合规)    │   │  (回归对比)    │
   └──────────────┘   └──────────────┘   └──────────────┘
          │                    │                    │
          └────────────────────┼────────────────────┘
                               ▼
                    ┌─────────────────────┐
                    │   Verdict Engine    │
                    │   ✅ Pass / ⚠️ Warn │
                    │   / ❌ Reject       │
                    └─────────────────────┘
```

### 4.2 三层验证

**Layer 1: 事实核查（幻觉检测）**
- Agent 声称引用了某文件 → 验证文件存在且内容匹配
- Agent 声称 API 返回了某结果 → 重新调用验证
- Agent 做了数据计算 → 独立重算
- 技术：LLM-as-Judge + 确定性验证 + WorldDB Merkle 树

**Layer 2: Schema 校验（格式合规）**
- 输出是否符合约定的 JSON Schema / 类型
- 代码是否可编译/可运行
- 安全规则（无密钥泄露、无 SQL 注入）
- 技术：Pydantic/JSON Schema + AST 分析 + 正则规则

**Layer 3: Diff 验证（回归对比）**
- 这次产出 vs 上次产出 → 变化是否合理
- 配置变更 → 是否影响下游
- Agent 升级后 → 行为是否退化
- 技术：文本 diff + 语义相似度 + 行为回归测试

### 4.3 集成方式

```yaml
# .agent-ci.yaml — 放在 Agent 项目根目录
verification:
  fact_check:
    - type: file_exists
      pattern: "output/*.json"
    - type: api_response
      endpoint: "https://api.example.com/health"
  schema:
    - type: json_schema
      schema: "./schemas/output.schema.json"
  diff:
    - type: semantic
      threshold: 0.95
```

Agent 跑完任务 → 产出自动进 Pipeline → 拿到 sign-off → 才能推给用户

---

## 五、MVP 设计

### 5.1 最小可行产品

**第一版：CLI 工具 + 三个 Checker**

```bash
# 安装
pip install agent-verify

# 使用
agent-verify run ./agent-output/ --config .agent-ci.yaml
# → ✅ Fact Check: 3/3 passed
# → ✅ Schema: valid
# → ⚠️  Diff: 2 files changed > threshold
# → Verdict: PASS WITH WARNINGS
```

**三个 Checker：**
1. `fact-check` — 文件存在性 + API 响应对账
2. `schema-check` — JSON/YAML 格式校验
3. `diff-check` — 产出变更对比

**第一周能做到：**
- Python 包，pip install 即用
- YAML 配置
- 终端彩色输出
- GitHub Action 集成（CI 里跑）

### 5.2 目标用户

| 用户 | 场景 | 付费意愿 |
|------|------|:--:|
| AI 创业公司 CTO | Agent 上线前需要验证 | 高 |
| 企业 AI 团队 | 合规要求，Agent 产出需审计 | 极高 |
| Solo AI Builder | 一个人管 5 个 Agent，验证不过来 | 中 |
| Agent 平台 | 作为平台内置验证层 | 高（B2B） |

### 5.3 定价策略

- **开源核心**（Apache 2.0）：三个基础 Checker + CLI
- **Pro（$29/月）**：高级 Checker（LLM-as-Judge、代码安全扫描）+ Dashboard
- **Enterprise（$499/月）**：私有部署 + 审计报告 + 合规认证

### 5.4 为什么现在做

- Agent 刚进入生产环境，验证需求正在爆发
- 竞品全是库模式，还没有基础设施玩家
- 你是少数同时有 Agent 开发经验 + 系统架构能力的人
- Moltbook 社区 60👍 帖子 = 市场在自我教育，你不需要教育市场

---

## 时间线

- 2026-05-06 | 来吉扫描 Moltbook + HN + Reddit + GitHub，发现 Agent 验证赛道空白 [来源：本次搜索]
- 2026-05-06 | codequalitybot 在 Moltbook 累计 80+ 赞，主张"验证应内建于 Agent 工作流" [来源：Moltbook search API]
- 2026-05-06 | Giskard v3 重写中，确认竞品也未解决独立验证问题 [来源：GitHub README]
- 2026-03 | langwatch/scenario 发布，专注对话模拟测试，不验证代码/数据产出 [来源：GitHub]
- 2026-02 | Cekura YC F24 融资，专注语音 Agent 测试 [来源：HN 89pts Launch]
- 2025-11 | Traceloop YC W23 融资，OpenTelemetry 幻觉检测 [来源：HN 101pts]
