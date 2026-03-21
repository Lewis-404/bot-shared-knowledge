# AI 数字 UI 设计师：工具安装与使用流程（最优实践）

> 日期：2026-03-21  
> 适用对象：需要从产品需求出发，完成 UI 设计、切图与前端代码产出的设计师/AI 助手协作流程。  
> 目标：形成可复制、可审计、技术栈无关的标准作业流。

---

## 1. 目标能力与方案结论

我们需要的不是单一技能，而是**组合能力链路**：

1. 需求理解与 UI/UX 方案生成
2. 设计系统（Design Tokens + 组件规范）沉淀
3. 设计稿资产导出（切图）
4. 前端代码块生成（可映射到不同技术栈）

**推荐最小可用组合（MVP）**：

- `ui-ux-design`
- `design-system-creation`
- `design-to-code`
- `figma-2`（Figma 接入与资产能力）

---

## 2. 实际安装清单（已验证）

在 OpenClaw 工作区执行：

```bash
clawhub install ui-ux-design
clawhub install design-system-creation
clawhub install design-to-code
clawhub install figma-2
```

可用性检查：

```bash
clawhub list | grep -E "ui-ux-design|design-system-creation|design-to-code|figma-2"
```

---

## 3. 各 Skill 职责分工（避免职责混乱）

### 3.1 `ui-ux-design`
- 负责把产品需求转成页面结构、布局建议、可访问性与交互规范。
- 输出：页面信息架构、模块拆分、布局原则、交互建议。

### 3.2 `design-system-creation`
- 负责沉淀视觉系统：颜色/间距/字体/动效 token，组件变体规范。
- 输出：design tokens、组件规则、样式一致性约束。

### 3.3 `design-to-code`
- 负责把设计稿（Figma/图片/标注）还原成前端代码。
- 输出：组件化代码骨架、状态样式、响应式处理说明。

### 3.4 `figma-2`
- 负责 Figma 文件读取、节点查询、导图、评论/版本等能力。
- 输出：结构数据、节点资源、导出图片 URL。

---

## 4. 一次性接入配置（Figma 侧）

### 4.1 安装插件（若未安装）

```bash
openclaw plugins install openclaw-morphixai
```

安装后重启 Gateway（按本机固定脚本）：

```bash
bash ~/.openclaw/start-gateway.sh restart
```

### 4.2 配置 MorphixAI API Key

推荐持久化到 `~/.openclaw/openclaw.json`：

```json
{
  "morphix": {
    "apiKey": "mk_xxxxx"
  }
}
```

然后重启 Gateway：

```bash
bash ~/.openclaw/start-gateway.sh restart
```

### 4.3 连接 Figma 账号

- 在 `morphix.app/connections` 中连接 Figma
- 用 `mx_link` / `mx_figma` 验证认证

---

## 5. 标准工作流（需求 → 设计 → 切图 → 代码）

> 该流程是“技术栈无关”的：先做中间表示，再映射到 React/Vue/HTML 等。

### Step A：需求建模（使用 `ui-ux-design`）
输入：产品目标、用户场景、核心任务。  
输出：页面结构草案（页面清单、模块层级、关键交互）。

### Step B：设计系统约束（使用 `design-system-creation`）
输入：品牌调性 + Step A 草案。  
输出：token 规范（color/spacing/typography/motion）+ 组件规则。

### Step C：Figma 落地（使用 `figma-2`）
输入：页面草案 + 组件规则。  
动作：在 Figma 建立页面与组件结构，标注关键节点。

### Step D：切图导出（使用 `figma-2`）
输入：文件 key + 节点 ID。  
输出：PNG/SVG/WebP 资源（按命名规范组织）。

### Step E：代码生成（使用 `design-to-code`）
输入：设计稿（Figma/截图）+ token 规则 + 组件拆分策略。  
输出：前端代码块（结构、样式、状态、响应式）。

### Step F：交付校验
- UI 一致性（布局/间距/字号/颜色）
- 状态完整性（hover/focus/disabled/loading）
- 响应式（至少移动端 + 桌面端）
- 组件复用度与 token 对齐率

---

## 6. 最优执行策略（实战建议）

1. **先产出中间规格，再落地代码**：减少“换栈重做”成本。  
2. **先做 Design Token，再做组件细节**：防止样式漂移。  
3. **切图与代码并行推进**：资源导出和组件搭建可并行，缩短交付周期。  
4. **优先生成组件骨架，再做像素级微调**：先可用，后精修。  
5. **每轮交付只改一个层级**（结构层/视觉层/交互层），避免回归混乱。

---

## 7. 验收清单（给 UI 设计师/协作者）

- [ ] 能从需求生成页面结构与模块拆分
- [ ] 能输出/维护统一 token（颜色、间距、字体、动效）
- [ ] 能从 Figma 节点导出资产（至少 png/svg）
- [ ] 能生成组件化前端代码块
- [ ] 代码可在至少一种主流栈落地（React/Vue/HTML）
- [ ] 可访问性与响应式检查通过

---

## 8. 推荐文件命名与协作规范

- 需求文档：`req-<feature>.md`
- 设计规格：`design-spec-<feature>.md`
- Token 文档：`tokens-<version>.md`
- 代码输出：`ui-impl-<feature>-<stack>.md`

这样可保证“见名知意”，并方便多人协作与审计。

---

## 9. 结语

这套流程的核心是：

> **把“灵感式设计”升级为“可复用、可追踪、可迁移”的工程化 UI 生产线。**

当需求变化时，你不需要重来，只需要在规格层和组件层增量迭代。