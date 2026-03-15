# Agency Agents × OpenClaw 集成指南

> 原项目：https://github.com/msitarzewski/agency-agents  
> 整理日期：2026-03-15  
> 适用场景：OpenClaw 用户按需安装专业 Agent

## 项目简介

**Agency Agents** 是一个包含 **147 个专业 AI Agent** 的开源集合，每个 Agent 都有：
- 🎯 专业领域（前端、后端、营销、测试、设计等 12 个大类）
- 🧠 独特个性和沟通风格
- 📋 具体工作流程和交付物模板
- ✅ 生产级的成功指标

每个 Agent 都是经过实战打磨的专家人格，不是简单的 Prompt 模板。

## 为什么需要按需安装

### 问题：一次性安装全部 147 个 Agent 的弊端

1. **配置文件污染**：每个 Agent 注册都会修改 `openclaw.json`，147 次修改会让配置文件变得臃肿
2. **Agent 列表噪音**：`openclaw agents list` 会显示 147 个 Agent，找到需要的 Agent 变得困难
3. **资源浪费**：大部分 Agent 可能永远不会用到（例如游戏开发者不需要金融分析师）
4. **维护成本**：更新、调试时需要处理大量无关 Agent

### 解决方案：按需精选安装

- 根据自己的专业领域选择 5-15 个核心 Agent
- 保持 Agent 列表简洁、可控
- 需要时随时扩展，灵活调整

## 安装步骤

### 1. 克隆项目并生成 OpenClaw 格式

```bash
cd /tmp
git clone --depth 1 https://github.com/msitarzewski/agency-agents.git
cd agency-agents

# 生成 OpenClaw 格式的 Agent workspace
./scripts/convert.sh --tool openclaw
```

生成的文件位于：`integrations/openclaw/`，每个 Agent 包含：
- `SOUL.md`：Agent 的个性、规则、沟通风格
- `AGENTS.md`：核心使命、工作流程、交付物模板
- `IDENTITY.md`：Agent 的身份标识

### 2. 浏览可用 Agent

```bash
ls integrations/openclaw/
```

**147 个 Agent 分类**：
- **engineering**（25 个）：前端、后端、移动端、DevOps、安全、测试等
- **design**（8 个）：UI/UX、品牌、视觉设计等
- **marketing**（26 个）：内容营销、SEO、社交媒体、邮件营销等
- **product**（5 个）：产品经理、用户研究、数据分析等
- **sales**（8 个）：销售策略、客户成功、提案撰写等
- **project-management**（6 个）：敏捷、Scrum、项目协调等
- **testing**（6 个）：QA、自动化测试、性能测试等
- **support**（6 个）：客户支持、技术支持、社区管理等
- **game-development**（10 个）：Unity、Unreal、游戏设计等
- **spatial-computing**（6 个）：AR/VR、3D 建模等
- **paid-media**（7 个）：广告投放、竞价优化等
- **specialized**（24 个）：AI 工程、区块链、数据科学等

完整列表见：https://github.com/msitarzewski/agency-agents#-the-agency-roster

### 3. 精选安装（推荐）

#### 示例：全栈工程师核心 Agent

```bash
# 复制选定的 Agent workspace
cd /tmp/agency-agents/integrations/openclaw
cp -r \
  frontend-developer \
  backend-architect \
  mobile-app-builder \
  devops-automator \
  api-tester \
  performance-benchmarker \
  code-reviewer \
  technical-writer \
  ai-engineer \
  ~/.openclaw/agency-agents/

# 批量注册到 OpenClaw
AGENTS=(
  "frontend-developer"
  "backend-architect"
  "mobile-app-builder"
  "devops-automator"
  "api-tester"
  "performance-benchmarker"
  "code-reviewer"
  "technical-writer"
  "ai-engineer"
)

for agent in "${AGENTS[@]}"; do
  openclaw agents add "$agent" \
    --workspace ~/.openclaw/agency-agents/"$agent" \
    --model bytecat/claude-opus-4-6
done

# 验证安装
openclaw agents list | grep -E "(frontend-developer|backend-architect)"
```

**配置 subagent 白名单**（必须步骤）：

注册完 Agent 后，需要配置白名单才能通过 `sessions_spawn` 调用：

```bash
# 配置 main agent 的 subagent 白名单
openclaw config set agents.list.0.subagents.allowAgents '[
  "frontend-developer",
  "backend-architect",
  "mobile-app-builder",
  "devops-automator",
  "api-tester",
  "performance-benchmarker",
  "code-reviewer",
  "technical-writer",
  "ai-engineer"
]'

# ⚠️ 重要：必须重启 Gateway 才能生效
# 
# 如果你有自定义的 start-gateway.sh 脚本（带代理配置），必须使用它：
bash ~/.openclaw/start-gateway.sh restart
#
# 否则使用官方命令：
openclaw gateway restart
#
# 注意：自定义脚本会彻底清理所有旧进程，避免多个 Gateway 同时运行

# 验证白名单配置
agents_list
# 应该看到所有 9 个 Agent 都在 "agents" 列表里
```

**白名单说明**：
- `agents.list.0` 是 `main` agent 的配置
- `subagents.allowAgents` 控制哪些 Agent 可以被 `sessions_spawn` 调用
- 默认只允许调用自己，需要手动添加其他 Agent
- 配置后必须重启 Gateway 才能生效

#### 其他领域推荐

**产品经理**：
- product-strategist
- user-researcher
- data-analyst
- technical-writer
- ux-designer

**营销人员**：
- content-strategist
- seo-specialist
- social-media-manager
- email-marketer
- copywriter

**设计师**：
- ui-designer
- ux-designer
- brand-designer
- motion-designer
- accessibility-auditor

**数据科学家**：
- data-scientist
- ml-engineer
- ai-engineer
- data-analyst
- database-optimizer

### 4. 使用 Agent

安装后，通过 `sessions_spawn` 调用专业 Agent：

```bash
# 前端性能优化
sessions_spawn(
  runtime="subagent",
  agentId="frontend-developer",
  task="优化这个 React 组件的渲染性能，目标 LCP < 2.5s"
)

# 后端架构设计
sessions_spawn(
  runtime="subagent",
  agentId="backend-architect",
  task="设计一个支持 10 万 QPS 的订单系统架构"
)

# 代码审查
sessions_spawn(
  runtime="subagent",
  agentId="code-reviewer",
  task="审查这个 PR，重点关注安全漏洞和性能瓶颈"
)

# API 测试
sessions_spawn(
  runtime="subagent",
  agentId="api-tester",
  task="为这个 REST API 生成完整的测试用例和 Postman collection"
)
```

## Agent 工作原理

每个 Agent 都有明确的：

### 1. 身份与个性（SOUL.md）
```markdown
## 🧠 Your Identity & Memory
- Role: Modern web application and UI implementation specialist
- Personality: Detail-oriented, performance-focused, user-centric
- Memory: You remember successful UI patterns and optimization techniques

## 🚨 Critical Rules You Must Follow
- Implement Core Web Vitals optimization from the start
- Follow WCAG 2.1 AA guidelines for accessibility
- Use modern performance techniques (code splitting, lazy loading)

## 💭 Your Communication Style
- Be precise: "Implemented virtualized table reducing render time by 80%"
- Focus on UX: "Added smooth transitions for better engagement"
```

### 2. 核心使命与工作流（AGENTS.md）
```markdown
## 🎯 Your Core Mission
- Build responsive, performant web applications
- Implement pixel-perfect designs with modern CSS
- Optimize Core Web Vitals for excellent page performance

## 🔄 Your Workflow Process
1. Project Setup: Configure build optimization and testing
2. Component Development: Create reusable component library
3. Performance Optimization: Implement code splitting and lazy loading
4. Testing: Write comprehensive unit and integration tests

## 📋 Your Deliverable Template
[具体的交付物模板，包含代码示例]
```

### 3. 成功指标
- Page load times < 3s on 3G networks
- Lighthouse scores > 90 for Performance and Accessibility
- Component reusability rate > 80%
- Zero console errors in production

## 实战案例

### 案例 1：前端性能优化

**场景**：React 应用首屏加载时间 8 秒，需要优化到 3 秒以内

```bash
sessions_spawn(
  runtime="subagent",
  agentId="frontend-developer",
  task="分析并优化这个 React 应用的首屏加载性能，目标 3 秒内完成首屏渲染"
)
```

**Agent 输出**：
1. 性能分析报告（Lighthouse、Bundle Analyzer）
2. 优化方案（代码分割、懒加载、图片优化）
3. 实现代码（React.lazy、动态 import、WebP 图片）
4. 验证结果（优化前后对比、Core Web Vitals 指标）

### 案例 2：后端架构设计

**场景**：设计一个高并发的秒杀系统

```bash
sessions_spawn(
  runtime="subagent",
  agentId="backend-architect",
  task="设计一个支持 10 万 QPS 的秒杀系统架构，包含库存扣减、订单创建、支付流程"
)
```

**Agent 输出**：
1. 架构设计图（微服务拆分、数据流）
2. 技术选型（Redis、Kafka、MySQL）
3. 核心代码实现（库存预扣、分布式锁）
4. 压测方案（JMeter 脚本、性能指标）

### 案例 3：代码审查

**场景**：审查一个包含 20 个文件的 PR

```bash
sessions_spawn(
  runtime="subagent",
  agentId="code-reviewer",
  task="审查这个 PR，重点关注：1) SQL 注入风险 2) 性能瓶颈 3) 代码可维护性"
)
```

**Agent 输出**：
1. 安全问题清单（SQL 注入、XSS、CSRF）
2. 性能问题（N+1 查询、未加索引）
3. 代码质量建议（重复代码、命名规范）
4. 修改建议（具体代码示例）

## 与 Claude Code 的对比

| 维度 | Agency Agents | Claude Code |
|------|---------------|-------------|
| **定位** | 专业 Agent 集合 | IDE 集成的 AI 助手 |
| **Agent 数量** | 147 个专业 Agent | 需要自己编写 |
| **个性化** | 每个 Agent 有独特个性 | 通过 CLAUDE.md 定制 |
| **工作流** | 内置完整工作流程 | 需要在 Skills 中定义 |
| **交付物** | 标准化模板 | 自定义 |
| **使用方式** | sessions_spawn 调用 | 直接对话 |
| **适用场景** | 需要专业领域专家 | 日常编码辅助 |

**互补关系**：
- Claude Code 适合日常编码、快速迭代
- Agency Agents 适合需要专业深度的任务（架构设计、性能优化、安全审计）

## 最佳实践

### 1. 选择合适的 Agent

**不要**：
- ❌ 安装全部 147 个 Agent
- ❌ 用通用 Agent 处理专业任务
- ❌ 在简单任务上调用专业 Agent（杀鸡用牛刀）

**应该**：
- ✅ 根据自己的专业领域选择 5-15 个核心 Agent
- ✅ 复杂任务用专业 Agent，简单任务用主 Agent
- ✅ 定期回顾，移除不常用的 Agent

### 2. 任务描述要具体

**不好的任务描述**：
```bash
task="帮我优化一下代码"
```

**好的任务描述**：
```bash
task="优化这个 React 组件的渲染性能，目标：
1. 首次渲染时间 < 100ms
2. 列表滚动帧率 > 60fps
3. 内存占用 < 50MB
当前问题：列表有 10000 条数据，滚动时卡顿严重"
```

### 3. 利用 Agent 的交付物模板

每个 Agent 都有标准化的交付物模板，可以直接要求：

```bash
task="按照 frontend-developer 的交付物模板，输出完整的性能优化报告"
```

### 4. 组合使用多个 Agent

复杂项目可以串联多个 Agent：

```bash
# 1. 架构设计
sessions_spawn(agentId="backend-architect", task="设计订单系统架构")

# 2. API 设计
sessions_spawn(agentId="api-designer", task="基于架构设计 RESTful API")

# 3. 性能测试
sessions_spawn(agentId="performance-benchmarker", task="压测 API 性能")

# 4. 安全审计
sessions_spawn(agentId="security-engineer", task="审计 API 安全性")
```

## 扩展与维护

### 添加新 Agent

```bash
# 1. 复制 workspace
cp -r /tmp/agency-agents/integrations/openclaw/new-agent ~/.openclaw/agency-agents/

# 2. 注册
openclaw agents add new-agent \
  --workspace ~/.openclaw/agency-agents/new-agent \
  --model bytecat/claude-opus-4-6

# 3. 验证
openclaw agents list | grep new-agent
```

### 移除不用的 Agent

```bash
# 1. 注销
openclaw agents remove old-agent

# 2. 删除 workspace
rm -rf ~/.openclaw/agency-agents/old-agent
```

### 更新 Agent

```bash
# 1. 拉取最新版本
cd /tmp/agency-agents
git pull
./scripts/convert.sh --tool openclaw

# 2. 覆盖旧版本
cp -r integrations/openclaw/frontend-developer ~/.openclaw/agency-agents/

# 3. 重启 OpenClaw Gateway
bash ~/.openclaw/start-gateway.sh restart
```

## 常见问题

### Q1: Agent 调用失败，提示 "Agent not found"

**原因**：Agent 未正确注册到 `openclaw.json`

**解决**：
```bash
openclaw agents list  # 检查 Agent 是否存在
openclaw agents add <agent-id> --workspace ~/.openclaw/agency-agents/<agent-id>
```

### Q2: Agent 输出质量不稳定

**原因**：任务描述不够具体，或者选错了 Agent

**解决**：
- 提供更详细的任务描述（目标、约束、当前问题）
- 检查是否选择了合适的 Agent（例如用 frontend-developer 处理后端任务）

### Q3: 如何查看 Agent 的能力范围

**方法 1**：查看 AGENTS.md
```bash
cat ~/.openclaw/agency-agents/frontend-developer/AGENTS.md
```

**方法 2**：查看原始文档
```bash
# 浏览器打开
open https://github.com/msitarzewski/agency-agents/blob/main/engineering/engineering-frontend-developer.md
```

### Q4: 可以修改 Agent 的个性吗

**可以**，直接编辑 `SOUL.md` 和 `AGENTS.md`：

```bash
# 编辑个性
vim ~/.openclaw/agency-agents/frontend-developer/SOUL.md

# 重启 Gateway 生效
bash ~/.openclaw/start-gateway.sh restart
```

## 参考资源

- **项目主页**：https://github.com/msitarzewski/agency-agents
- **完整 Agent 列表**：https://github.com/msitarzewski/agency-agents#-the-agency-roster
- **OpenClaw 集成文档**：https://github.com/msitarzewski/agency-agents/blob/main/integrations/openclaw/README.md
- **中文翻译版**：https://github.com/jnMetaCode/agency-agents-zh（100+ Agent 已翻译）
- **社区讨论**：https://github.com/msitarzewski/agency-agents/discussions

## 总结

Agency Agents 为 OpenClaw 用户提供了 **147 个开箱即用的专业 Agent**，每个 Agent 都是经过实战打磨的领域专家。

**核心价值**：
1. **专业深度**：每个 Agent 都有明确的专业领域和工作流程
2. **标准化交付**：统一的交付物模板，输出质量可预期
3. **灵活组合**：按需安装，随时扩展
4. **开源免费**：MIT 协议，可商用

**推荐做法**：
- 根据自己的专业领域精选 5-15 个核心 Agent
- 复杂任务用专业 Agent，简单任务用主 Agent
- 定期回顾和优化 Agent 列表

**下一步**：
1. 浏览 [完整 Agent 列表](https://github.com/msitarzewski/agency-agents#-the-agency-roster)
2. 选择适合自己的 Agent
3. 按照本文档的步骤安装
4. 在实际项目中使用并反馈

---

**文档版本**：v1.0  
**更新日期**：2026-03-15  
**贡献者**：来吉 (Laji)  
**许可协议**：MIT
