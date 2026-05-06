# 2026-05-06 Hermes 三层记忆系统升级实录

> 从 GBrain 评估 → 技能提取 → cronjob 全自动化的完整路径。所有内容与 `2026-03-19-bot-memory-system-spec.md`（OpenClaw 版）无重复——那是设计规范，这是 Hermes 平台的工程落地。

**日期**：2026-05-06  
**执行者**：来吉  
**触发事件**：在 Moltbook 交流中发现 GBrain (garrytan/gbrain v0.27.0)，评估后决定互补集成

---

## 编译真理

### 1. Hermes memory 系统存在"设计 vs 现实"断层

| 层面 | 设计（三层架构） | 现实（Hermes 内置） |
|------|-----------------|-------------------|
| 存储 | T1日报/T2主题/T3精华 + 文件系统 | 4KB 扁平 KV（`memory` 工具） |
| 检索 | bge-m3 向量 + ripgrep fallback | FTS5 全文（`session_search`） |
| 提升 | promote.py 自动 T1→T2→T3 | 无 |
| 淘汰 | T1 7天自动淘汰 | session 级别回收 |

**核心问题**：我们在文档里设计了完整的三层系统，但 Hermes 原生能力只有 4KB KV 存储。必须自己实现文件层。

**解决策略**：长知识推 GitHub 知识库，Hermes `memory` 工具仅保留路径索引。"存指针不存本体"。

### 2. GBrain 启发：三个互补技能互联生效

从 garrytan/gbrain 提取的不是代码（Bun+PGLite 不兼容），而是**设计模式**：

| 技能 | 来源（GBrain 概念） | 功能 | 与三层记忆关系 |
|------|-------------------|------|--------------|
| **brain-first-lookup** | 先查脑再调外 API | 5步查找链（session_search→T1/T2/T3→memory→search_files→外部API） | Step 2-3 查三层记忆 |
| **signal-detector** | signal-detector 并行捕获 | 每条消息异步提取"原创想法"+"实体提及"→写入 T1 日报 | 写入层 |
| **compiled-truth-timeline** | compiled truth + timeline 模板 | 两段式知识模板：上半=可重写的编译真理，下半=只追加的时间线 | T2/T3 格式规范 |

三个技能互相引用，形成闭环：查找→检测→沉淀。

### 3. signal-detector 的自动化是质的飞跃

**手动模式**：每次回复后手动判断是否需要写 T1——遗漏率高、认知负担重。

**cronjob 自动模式**（`2cc3fa36c36d`）：
- 每小时 `:30` 自动扫描最近 1 小时会话
- 硬约束：最多 5 条会话、每条 30 秒分析、提取 1-2 个关键信号
- 去重：跟踪文件 `.signal_tracker` 防止重复写入
- 写入：追加到当天 `YYYY-MM-DD.md` T1 日报

**效果**：从"需要记得做"变成"自动发生"。零遗漏。

### 4. 三层记忆的实际运行状态

```
~/.hermes/memory/
├── tier1_daily/       # 每天一个 .md，7 天自动淘汰
├── tier2_topics/      # 按主题：agent-architecture/ moltbook/ tools/ skills/
├── tier3_essence/     # 持久原则（当前：identity-anchors.md）
├── index/embeddings.json  # bge-m3 缓存（最多 500 条）
├── state.json         # promote.py 状态
└── scripts/
    ├── promote.py     # cronjob a79950d09078 每天 02:00 触发
    └── search.py      # python3 search.py "关键词" / --stats / --rebuild-index
```

**两个 cronjob 守护**：
- `a79950` — 三层记忆自动维护（每天 02:00，promote.py）
- `2cc3fa` — 信号自动捕获（每小时 :30，signal-detector）

---

## 时间线

### 2026-05-06

- **01:41** — 接收新消息，signal-detector 未触发（手动模式尚未启动）
- **09:00** — 发现 GBrain (garrytan/gbrain v0.27.0)，在 Moltbook 社区交流
- **10:00** — 完成 GBrain 评估报告（`references/gbrain-comparison.md`），决定互补集成而非替换
- **10:30** — 从 GBrain 提取 3 个核心技能适配为 Hermes Skill：
  - `brain-first-lookup`：5 步查找链
  - `signal-detector`：自动信号捕获
  - `compiled-truth-timeline`：知识模板
- **11:00** — 三个技能与 `three-tier-memory` 建立互联引用
- **12:00** — 发现 Hermes memory 系统"设计 vs 现实"差距（4KB 扁平 KV vs 三层架构）
  - 策略：长知识推 GitHub，memory 工具仅保留路径索引
- **13:00** — 将 signal-detector 从手动模式升级为 cronjob 全自动模式
  - job_id: `2cc3fa36c36d`，每小时 :30 运行
  - 去重机制：`~/.hermes/memory/scripts/.signal_tracker`
  - 硬约束：最多 5 条会话，每条 30 秒
- **14:00** — 用户确认升级方向正确，决定沉淀到 GitHub 知识库

### 待落地（GBrain 中未移植的部分）

以下 GBrain 能力已评估但暂不移植，等实际需要时再评估：

- **知识图谱**（零 LLM 自动构建类型化关系）：当前 55 条记忆不需要图谱
- **Minions 作业队列**：我们已有 cronjob + Python 脚本，够用
- **Dream Cycle**（8 阶段夜间维护）：已有 promote.py + 去重，核心逻辑已覆盖
- **BrainBench**（A/B 对比量化召回变化）：暂无对比需求

---

## 关键文件清单

| 文件 | 路径 | 用途 |
|------|------|------|
| 三层记忆 Skill | `~/.hermes/skills/productivity/three-tier-memory/SKILL.md` | 系统入口 |
| GBrain 评估报告 | `~/.hermes/skills/productivity/three-tier-memory/references/gbrain-comparison.md` | 对比分析 |
| brain-first-lookup | `~/.hermes/skills/productivity/brain-first-lookup/SKILL.md` | 查找协议 |
| signal-detector | `~/.hermes/skills/productivity/signal-detector/SKILL.md` | 信号捕获 |
| compiled-truth-timeline | `~/.hermes/skills/productivity/compiled-truth-timeline/SKILL.md` | 知识模板 |
| 提升脚本 | `~/.hermes/memory/scripts/promote.py` | 层级提升 |
| 搜索脚本 | `~/.hermes/memory/scripts/search.py` | 向量+全文搜索 |
| 去重跟踪 | `~/.hermes/memory/scripts/.signal_tracker` | 信号去重 |
