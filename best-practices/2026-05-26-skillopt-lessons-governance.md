# SkillOpt 设计原则落地方案

> 2026-05-26，基于微软 SkillOpt 论文（arxiv 2605.23904）的核心洞察，
> 在来财的多 bot 工作流中落地了三项改进。

## 论文核心洞察

> 把 agent skill 当外部状态训练，分离 optimizer 和 executor，
> 用 bounded edit + validation gate 控制更新。

### 与我们的系统高度共振

| 论文概念 | 我们的对应 |
|----------|-----------|
| Optimizer | 来财（管家）— 负责提炼经验、生成规则修改建议 |
| Executor | 其他 9 个专业 bot — 执行具体任务 |
| Validation Gate | 主人 — 纠正、确认、拍板 |
| Rejected-Edit Buffer | `memory/lessons/rejected/` — 记录失败方向，避免重复 |

## 落地的三项改进

### 1. Validation Gate 显式化

修改核心规则文件（AGENTS.md / SOUL.md / MEMORY.md）前，必须满足：

- 同一主题在 `lessons/confirmed/` 或日记中积累 **≥3 条独立证据**
  - 证据来源：主人纠正 / 实践中验证 / 跨 session 复现
- 否则只写入 lessons/ 或日记，不修改核心规则
- 例外：主人直接要求修改 → 立即执行，事后补记日记

**设计意图**：防止单次错误或噪音直接污染核心规则，用证据积累替代冲动修改。

### 2. Rejected-Edit Buffer 三级分级

`memory/lessons/` 不再扁平存放，改为三级目录：

```
memory/lessons/
├── confirmed/     ← 已确认有效，active，默认召回，可晋升到核心规则
├── rejected/      ← 已证明无效/被替代，superseded/archived，仅追溯召回
└── provisional/   ← 待验证，provisional/stale，降权召回，不用于修改核心规则
```

**操作纪律**：

- 来财（optimizer）生成修改建议前，必须先查 `rejected/` 避免重复无效方向
- 同一方向被拒 ≥2 次，写入 `rejected/` 并附原因
- provisional 积累 ≥3 条独立证据后，可晋升到 confirmed/

**状态流转**：

```
新教训 → provisional/（待验证）
           ↓ ≥3 条证据
        confirmed/（可晋升）
           ↓
        核心规则修改

方向失败 → rejected/（已拒绝）
           ↓ ≥2 次失败
        永久归档，不再尝试
```

### 3. Bounded Update + Cooldown

- 任一核心规则文件（AGENTS.md / SOUL.md）同一主题修改后，**7 天内不再追加修改同一主题**
- 新 rule 上线后先观察至少一次完整任务周期（主人使用→bot 执行→反馈闭环）再评估
- 紧急修正（安全漏洞、功能阻断）不受 cooldown 限制，但需在日记中标注原因

**设计意图**：防止 optimizer 在短期内反复微调同一规则，给执行反馈留足够时间。

## 为什么不做一个完整的 SkillOpt 循环？

有三个现实约束：

1. **缺少自动评分机制**：bot 任务高度异构（后端开发 vs 设计 vs 运维），无法用统一指标自动打分
2. **Validation Gate 是人**：主人是最终裁判，自动化程度不需要太高
3. **过拟合风险**：全自动循环可能让 skill 收敛到局部最优，反而降低适应性

因此落地的是"半自动 SkillOpt"：来财收集证据、生成建议，主人做 validation gate，bounded update 控制节奏。

## 效果验证

上线后首次应用（同日 11:55）：

- 按五维标准审计了 browser-automation（11/25）+ bot-memory-audit（16/25）
- 不是直接改核心规则，而是先写入日记 + provisional/
- 经主人确认后执行修复
- 修复后预估：browser 17-19/25，bot-memory-audit 20-22/25

## 相关文档

- [Skill 审计五维标准](2026-05-26-skill-audit-five-dimensions.md)
- [Bot 记忆系统规范 v1.1](2026-03-19-bot-memory-system-spec.md)
- 论文：SkillOpt: Executive Strategy for Self-Evolving Agent Skills (arxiv 2605.23904)
