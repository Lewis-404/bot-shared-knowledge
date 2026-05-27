# Skill 审计五维标准

> 2026-05-26 确立。审核任何 skill 时按以下五个维度打分（1-5 分/项，满分 25 分）。

## 五个维度

### 1. 格式约束（Format Constraints）

**评分标准**：输出格式有没有「别写废话」的规定

| 分数 | 标准 |
|------|------|
| 1 | 无任何输出纪律要求 |
| 2 | 有模糊建议（"尽量简洁"） |
| 3 | 有明确指令（"直接给结果，不要解释过程"） |
| 4 | 有结构化输出模板 + 禁止叙述性语言 |
| 5 | 输出格式与下游系统对接（JSON schema / 固定模板） |

**最佳实践**：lead with outcome, avoid narration。skill 的第一步就声明输出格式预期。

### 2. 证据绑定（Evidence Binding）

**评分标准**：查到的东西要不要独立验证后才用

| 分数 | 标准 |
|------|------|
| 1 | 查到什么就用什么，不验证 |
| 2 | 对部分来源做简单检查 |
| 3 | 对关键信息做独立验证（screenshot, readback, cross-check） |
| 4 | 系统性地对所有外部信息做验证 |
| 5 | 验证链完整且可审计（保留验证证据） |

**最佳实践**：screenshot 验证页面状态，readback 验证文件写入，cross-check 多源交叉验证。

### 3. 失败恢复（Failure Recovery）

**评分标准**：一个步骤挂了，下一步做什么

| 分数 | 标准 |
|------|------|
| 1 | 没有任何错误处理 |
| 2 | 只有 try-catch，重试就报错 |
| 3 | 有重试链（retry chain）+ 降级路径 |
| 4 | retry chain + fallback path + escalation criteria 完整 |
| 5 | 上述全部 + 自愈尝试 + 自动上报 |

**最佳实践**：
1. 第 1 次失败 → 重试（换参数）
2. 第 2 次失败 → 降级（换方案）
3. 第 3 次失败 → 上报（告诉主人，不要死循环）

### 4. 验证纪律（Verification Discipline）

**评分标准**：任务完成后有没有自检清单

| 分数 | 标准 |
|------|------|
| 1 | 没有验证步骤，"做完了"就算 |
| 2 | 有口头验证描述（"检查一下"） |
| 3 | 有明确的验证项列表 |
| 4 | 每个步骤都有对应的验证方法（completion gate） |
| 5 | 验证结果可追溯，失败自动触发回滚 |

**最佳实践**：completion gate with explicit check items。
```
✅ 验证清单：
1. 文件是否存在 → ls -la
2. 内容是否正确 → grep 关键字段
3. 服务是否响应 → curl health check
4. 下游是否正常 → 端到端测试
5. 回滚是否就绪 → 确认备份存在
```

### 5. 决策规则（Decision Rules）

**评分标准**：多个选项时按什么规则选

| 分数 | 标准 |
|------|------|
| 1 | 没有决策规则，凭感觉选 |
| 2 | 有优先级声明但不具体 |
| 3 | 有明确的决策树 / 分支逻辑 |
| 4 | 有 trigger conditions + tiebreakers |
| 5 | 决策可解释，能回溯为什么选 A 不选 B |

**最佳实践**：clear branching logic with trigger conditions and tiebreakers。
```
决策树：
- 文件 < 100MB → 直接读取
- 100MB-1GB → 流式读取
- > 1GB → 分片处理
- 超时 → 降级方案 B
```

## 参考基线

| Skill | 得分 | 评级 |
|-------|------|------|
| otc-confirmation | 24/25 | 🏆 当前最佳 |
| autonomous-executor | 22/25 | 🥈 优秀 |
| bot-memory-audit | 20-22/25 | 🥉 良好（5/26 修复后） |
| browser-automation | 17-19/25 | ⚠️ 待提升（5/26 修复后） |

## 使用方式

1. 收到新 skill 或审计现有 skill 时，按五维逐一打分
2. 低于 15 分的标记为 P0，15-20 分标记为 P1
3. 修复后重新评分，记录改善幅度
4. 审计结果写入日记，晋升到 confirmed/ 后可作为核心规则修改依据

## 相关文档

- [SkillOpt 设计原则落地方案](2026-05-26-skillopt-lessons-governance.md)
- [Bot 记忆系统规范 v1.1](2026-03-19-bot-memory-system-spec.md)
