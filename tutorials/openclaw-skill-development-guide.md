# OpenClaw Skill 开发完整指南

> 整理日期：2026-03-15  
> 适用对象：OpenClaw 用户、Skill 开发者  
> 难度：中级

## 目录

- [什么是 Skill](#什么是-skill)
- [核心概念](#核心概念)
- [目录结构](#目录结构)
- [SKILL.md 格式](#skillmd-格式)
- [三大核心原则](#三大核心原则)
- [开发步骤](#开发步骤)
- [最佳实践](#最佳实践)
- [常见问题](#常见问题)
- [参考资源](#参考资源)

---

## 什么是 Skill

**Skill** 是 OpenClaw 的模块化扩展包，用于教会 Agent 如何使用特定工具、执行专业工作流、或掌握领域知识。

可以把 Skill 理解为"给 Agent 的入职培训手册"——它将通用 Agent 转变为具备特定领域专业知识的专家。

### Skill 提供什么

1. **专业工作流**：多步骤的领域特定流程
2. **工具集成**：如何使用特定文件格式或 API
3. **领域专业知识**：公司特定知识、业务逻辑、数据模式
4. **打包资源**：脚本、参考文档、模板等复杂任务所需资源

---

## 核心概念

### Skill = 目录 + SKILL.md + 可选资源

- **SKILL.md**（必需）：包含元数据（YAML frontmatter）和指令（Markdown）
- **可选资源**：scripts/（脚本）、references/（文档）、assets/（模板/图标）

### 加载机制

OpenClaw 从三个位置加载 Skill（优先级从高到低）：

1. **Workspace skills**：`<workspace>/skills`（每个 Agent 独有）
2. **Managed skills**：`~/.openclaw/skills`（所有 Agent 共享）
3. **Bundled skills**：OpenClaw 内置 Skill

同名 Skill 按优先级覆盖。

### 触发机制

- **frontmatter 的 `description`** 决定何时触发 Skill
- 只有触发后才加载 SKILL.md 正文到上下文
- references/ 中的文档按需引用，不自动加载

---

## 目录结构

```
skill-name/
├── SKILL.md              # 必需：元数据 + 指令
├── CHANGELOG.md          # 推荐：版本变更记录
├── README.md             # 推荐：安装和使用说明
└── 可选资源/
    ├── scripts/          # 可执行脚本（Python/Bash/Node.js）
    ├── references/       # 参考文档（按需加载到上下文）
    └── assets/           # 输出用文件（模板/图标/字体）
```

### 各目录用途

#### `scripts/`
存放可执行脚本，用于：
- 重复性任务（避免每次重写代码）
- 需要确定性的操作（避免 LLM 随机性）
- 复杂逻辑（减少 token 消耗）

**示例**：
```bash
scripts/
├── generate_code.sh      # 生成确认码
├── send_email.sh         # 发送邮件
└── verify_input.sh       # 验证用户输入
```

#### `references/`
存放参考文档，按需加载到上下文：
- API 文档
- 配置示例
- 最佳实践清单
- 故障排查指南

**示例**：
```bash
references/
├── api-reference.md      # API 完整文档
├── examples.md           # 使用示例
└── troubleshooting.md    # 常见问题
```

#### `assets/`
存放输出用文件：
- 代码模板
- 配置文件模板
- 图标、字体等静态资源

**示例**：
```bash
assets/
├── template.yaml         # 配置模板
├── icon.png              # Skill 图标
└── boilerplate.py        # 代码模板
```

---

## SKILL.md 格式

### 基本结构

```markdown
---
name: skill-name
description: 清晰描述这个 Skill 是什么、何时使用（这是触发条件）
---

# Skill 标题

## 核心指令
- 简洁明确的操作步骤
- 只写 Agent 不知道的信息
- 用例子代替冗长解释

## 工具使用
- 如何调用 scripts/ 中的脚本
- 如何引用 references/ 中的文档

## 示例
具体使用场景和预期输出
```

### Frontmatter 字段

#### 必需字段

```yaml
name: skill-name          # Skill 唯一标识（小写、连字符分隔）
description: |            # 触发条件描述（关键！）
  清晰描述这个 Skill 的功能和使用场景。
  Agent 根据这个描述判断是否加载此 Skill。
```

#### 可选字段

```yaml
version: 1.0.0            # 语义化版本号
author: Your Name         # 作者
license: MIT              # 许可协议
tags:                     # 标签（便于搜索）
  - automation
  - security
requires:                 # 依赖
  env:                    # 环境变量
    - API_KEY
  binaries:               # 二进制工具
    - curl
    - jq
```

### Description 编写技巧

**好的 description**（清晰、具体、包含触发词）：
```yaml
description: |
  Send emails via SMTP. Use when the user asks to send an email, 
  notify someone by email, or compose and deliver a message to 
  an email address.
```

**不好的 description**（模糊、泛泛）：
```yaml
description: Email tool
```

**关键点**：
- 说明功能（做什么）
- 说明触发场景（何时用）
- 包含用户可能使用的关键词

---

## 三大核心原则

### 1. 简洁至上（Token 效率）

**上下文窗口是公共资源**，Skill 与系统提示、对话历史、其他 Skill 共享。

**默认假设：Agent 已经很聪明**
- 只补充 Agent 不知道的信息
- 用例子代替冗长解释
- 挑战每一段文字："Agent 真的需要这个吗？"

**示例对比**：

❌ **冗长版本**（浪费 token）：
```markdown
## 如何发送邮件

首先，你需要理解 SMTP 协议的工作原理。SMTP（Simple Mail Transfer Protocol）
是一种用于发送电子邮件的协议。它使用 TCP 端口 25、465 或 587。在发送邮件之前，
你需要配置 SMTP 服务器地址、端口、用户名和密码...
```

✅ **简洁版本**（高效）：
```markdown
## 发送邮件

```bash
scripts/send_email.sh "收件人" "主题" "正文"
```

环境变量：SMTP_USER、SMTP_PASS、SMTP_SERVER
```

### 2. 匹配自由度（控制 vs 灵活）

根据任务的脆弱性和变化性，选择合适的自由度：

#### 高自由度（文本指令）
**适用场景**：
- 多种方案都可行
- 决策依赖上下文
- 启发式方法引导

**示例**：
```markdown
## 代码审查

检查以下方面：
- 安全漏洞（SQL 注入、XSS、CSRF）
- 性能问题（N+1 查询、内存泄漏）
- 代码风格（命名、注释、结构）

根据项目类型调整审查重点。
```

#### 中自由度（伪代码/带参数脚本）
**适用场景**：
- 有偏好模式但允许变化
- 配置影响行为
- 需要一定灵活性

**示例**：
```markdown
## 生成 API 文档

```bash
scripts/generate_docs.sh \
  --input src/ \
  --output docs/ \
  --format markdown \
  --include-examples
```

可选参数：
- `--format`: markdown | html | pdf
- `--include-examples`: 是否包含示例
```

#### 低自由度（具体脚本）
**适用场景**：
- 操作脆弱、易出错
- 一致性至关重要
- 必须遵循特定顺序

**示例**：
```markdown
## 部署到生产环境

**严格按以下顺序执行**：

```bash
# 1. 备份数据库
scripts/backup_db.sh

# 2. 停止服务
scripts/stop_service.sh

# 3. 部署新版本
scripts/deploy.sh

# 4. 运行迁移
scripts/migrate.sh

# 5. 启动服务
scripts/start_service.sh

# 6. 验证健康检查
scripts/health_check.sh
```

**不要跳过任何步骤，不要改变顺序。**
```

### 3. 按需加载（减少上下文污染）

**加载层级**：
1. **Frontmatter**：始终加载（用于判断是否触发）
2. **SKILL.md 正文**：触发后加载
3. **references/ 文档**：Agent 主动引用时加载

**设计建议**：
- 把常用指令放在 SKILL.md 正文
- 把详细文档放在 references/（按需引用）
- 避免在 SKILL.md 中重复 references/ 的内容

**示例**：
```markdown
# API Integration Skill

## 快速开始

```bash
scripts/api_call.sh GET /users
```

## 详细文档

需要完整 API 参考？查看 `references/api-reference.md`
需要认证配置？查看 `references/auth-setup.md`
```

---

## 开发步骤

### Step 1: 创建目录

```bash
# 在 workspace 中创建 Skill 目录
mkdir -p ~/.openclaw/workspace/skills/my-skill
cd ~/.openclaw/workspace/skills/my-skill
```

### Step 2: 编写 SKILL.md

创建 `SKILL.md` 文件：

```markdown
---
name: my-skill
description: |
  [清晰描述功能和触发场景]
  Use when [具体使用场景].
version: 1.0.0
author: Your Name
---

# My Skill

## 核心功能

[简洁的操作指令]

## 使用示例

[具体例子]
```

### Step 3: 添加资源（可选）

#### 添加脚本

```bash
mkdir -p scripts
cat > scripts/example.sh << 'EOF'
#!/bin/bash
echo "Hello from my skill!"
EOF
chmod +x scripts/example.sh
```

#### 添加参考文档

```bash
mkdir -p references
cat > references/api-docs.md << 'EOF'
# API Documentation

[详细 API 文档]
EOF
```

#### 添加模板

```bash
mkdir -p assets
cat > assets/template.yaml << 'EOF'
# Configuration Template
key: value
EOF
```

### Step 4: 测试

#### 方法 1：刷新 Skills

```bash
# 在 OpenClaw 中执行
openclaw agent --message "refresh skills"
```

#### 方法 2：重启 Gateway

```bash
bash ~/.openclaw/scripts/gateway.sh restart
```

#### 测试使用

```bash
openclaw agent --message "use my skill to do something"
```

### Step 5: 打包发布（可选）

如果要分享到 ClawHub：

```bash
# 打包 Skill
scripts/package_skill.py ~/.openclaw/workspace/skills/my-skill

# 会生成 my-skill.skill 文件（实际是 zip）
```

发布到 ClawHub：

```bash
clawhub publish my-skill.skill
```

### Step 6: 迭代优化

1. 在实际任务中使用 Skill
2. 发现问题或低效之处
3. 更新 SKILL.md 或资源
4. 重新测试
5. 更新版本号和 CHANGELOG.md

---

## 最佳实践

### 1. 安全第一

**防止命令注入**：
```markdown
❌ 不安全：
```bash
curl "$USER_INPUT"
```

✅ 安全：
```bash
# 验证输入
if [[ ! "$URL" =~ ^https?:// ]]; then
  echo "Invalid URL"
  exit 1
fi
curl "$URL"
```
```

**敏感信息处理**：
- 使用环境变量存储密钥
- 不在日志中输出密码
- 使用安全的临时文件（mode 600）

### 2. 版本管理

**语义化版本号**：
- `1.0.0` → 初始版本
- `1.0.1` → Bug 修复
- `1.1.0` → 新增功能（向后兼容）
- `2.0.0` → 破坏性变更

**CHANGELOG.md 示例**：
```markdown
# Changelog

## [1.1.0] - 2026-03-15
### Added
- 新增批量处理功能

### Fixed
- 修复邮件发送失败的问题

## [1.0.0] - 2026-03-01
### Added
- 初始版本发布
```

### 3. 文档完整

**README.md 应包含**：
- 功能简介
- 安装步骤
- 使用示例
- 环境变量配置
- 常见问题

**示例**：
```markdown
# My Skill

简短描述功能。

## 安装

```bash
clawhub install my-skill
```

## 配置

设置环境变量：
```bash
export API_KEY="your-key"
```

## 使用

[具体使用示例]

## 常见问题

Q: 如何处理认证失败？
A: 检查 API_KEY 是否正确设置。
```

### 4. 测试覆盖

**测试清单**：
- [ ] 正常场景测试
- [ ] 边界条件测试
- [ ] 错误处理测试
- [ ] 环境变量缺失测试
- [ ] 权限不足测试

### 5. Token 优化

**减少 token 消耗**：
- 用表格代替长段落
- 用代码块代替文字描述
- 把详细文档放在 references/
- 避免重复内容

**示例对比**：

❌ **高 token 消耗**：
```markdown
首先执行步骤 1，然后执行步骤 2，接着执行步骤 3...
```

✅ **低 token 消耗**：
```markdown
1. 步骤 1
2. 步骤 2
3. 步骤 3
```

---

## 常见问题

### Q1: Skill 没有被触发？

**检查清单**：
1. `description` 是否清晰描述了触发场景？
2. 用户的请求是否包含 `description` 中的关键词？
3. Skill 是否已刷新？（`refresh skills` 或重启 Gateway）
4. 是否有同名 Skill 覆盖了你的 Skill？

### Q2: 如何调试 Skill？

**方法 1：查看日志**
```bash
tail -f ~/.openclaw/logs/gateway.log
```

**方法 2：在 SKILL.md 中添加调试信息**
```markdown
## Debug

如果遇到问题，执行：
```bash
echo "Debug: Skill loaded"
```
```

### Q3: 如何在多个 Agent 间共享 Skill？

**方案 1：安装到 managed skills**
```bash
cp -r my-skill ~/.openclaw/skills/
```

**方案 2：使用 `skills.load.extraDirs`**

在 `~/.openclaw/openclaw.json` 中配置：
```json
{
  "skills": {
    "load": {
      "extraDirs": ["/path/to/shared/skills"]
    }
  }
}
```

### Q4: 如何处理 Skill 依赖？

**在 frontmatter 中声明**：
```yaml
requires:
  env:
    - API_KEY
    - SMTP_USER
  binaries:
    - curl
    - jq
```

**在 SKILL.md 中检查**：
```markdown
## 前置检查

```bash
if ! command -v curl &> /dev/null; then
  echo "Error: curl not found"
  exit 1
fi
```
```

### Q5: 如何更新已安装的 Skill？

**方法 1：使用 ClawHub**
```bash
clawhub update my-skill
```

**方法 2：手动替换**
```bash
rm -rf ~/.openclaw/workspace/skills/my-skill
cp -r /path/to/new/my-skill ~/.openclaw/workspace/skills/
```

---

## 参考资源

### 官方文档
- [Creating Skills](https://docs.openclaw.ai/tools/creating-skills)
- [Skills Configuration](https://docs.openclaw.ai/tools/skills-config)
- [AgentSkills Spec](https://agentskills.io)

### 示例 Skill
- [OTC Confirmation](https://clawhub.com/skills/otc-confirmation) - 一次性确认码机制
- [Apple Notes](https://clawhub.com/skills/apple-notes) - Apple Notes 管理
- [Weather](https://clawhub.com/skills/weather) - 天气查询

### 工具
- [ClawHub](https://clawhub.com) - Skill 注册中心
- [skill-creator](https://github.com/openclaw/openclaw/tree/main/skills/skill-creator) - Skill 创建助手

### 社区
- [OpenClaw Discord](https://discord.com/invite/clawd)
- [GitHub Discussions](https://github.com/openclaw/openclaw/discussions)

---

## 总结

开发一个 OpenClaw Skill 的核心要点：

1. **结构清晰**：SKILL.md（必需）+ 可选资源（scripts/references/assets）
2. **触发精准**：frontmatter 的 `description` 决定何时加载
3. **简洁高效**：只写 Agent 不知道的，用例子代替解释
4. **自由度匹配**：根据任务脆弱性选择高/中/低自由度
5. **按需加载**：详细文档放 references/，避免污染上下文
6. **安全第一**：防止命令注入，保护敏感信息
7. **持续迭代**：实际使用 → 发现问题 → 优化 → 再测试

**下一步**：
1. 选择一个你需要的功能
2. 按照本指南创建第一个 Skill
3. 在实际项目中使用并优化
4. 分享到 ClawHub 帮助其他人

---

**文档版本**：v1.0  
**更新日期**：2026-03-15  
**贡献者**：来财 (Laicai)  
**许可协议**：MIT

---

## 补充（2026-03-20）：SKILL.md 内容设计 5 模式（Google Cloud Tech）

> 结论：格式已趋同，真正的差异化来自 Skill 内部逻辑设计。

### 五种可组合模式

1. **Tool Wrapper（工具包装器）**
   - 用于按需加载特定库/框架上下文，减少 system prompt 负担。

2. **Generator（生成器）**
   - 用于稳定生成结构化输出；`assets/` 放模板，`references/` 放风格规范。

3. **Reviewer（评审者）**
   - 将 rubric/checklist 模块化到 `references/`，按严重等级输出评审结果。

4. **Inversion（反转/访谈）**
   - 先提问收集完整信息，再生成；通过 gating 避免“信息不足时乱猜”。

5. **Pipeline（流水线）**
   - 复杂任务拆成多阶段 + checkpoint，阶段内按需加载上下文，提升可控性。

### 组合建议（实战）

- Pipeline 内嵌 Reviewer（每阶段质量门禁）
- Generator 前置 Inversion（先问清再填模板）
- Tool Wrapper 作为任意阶段的上下文注入层

### 对本指南的升级点

- 不再把 SKILL.md 仅视为“说明文档”，而视为“可编排工作流”。
- 新 Skill 设计建议明确：
  - 是否需要 Inversion（先问后做）
  - 是否需要 Pipeline（分阶段确认）
  - 是否需要 Reviewer（可验证评分）

