# 新增一个 Discord Bot 的标准流程（OpenClaw）

> 更新时间：2026-03-20
> 适用范围：Lewis 当前本地 OpenClaw 多 Profile 架构

---

## 1. 目标与原则

目标：在不影响现有 Bot 稳定性的前提下，新增一个可独立运行的 Discord Bot。  
原则：**先规划、再接入、后验证**，全程可回滚。

---

## 2. 前置准备

### 2.1 Discord 侧准备

1. 在 Discord Developer Portal 创建应用
2. 添加 Bot，获取 Token
3. 配置必要 Intents（按业务最小权限）
4. 将 Bot 邀请进目标服务器/频道（如需）

### 2.2 OpenClaw 侧准备

1. 明确新 bot 的角色定位（避免和现有 bot 职责重叠）
2. 规划 profile 名称（建议 `<role>-bot`）
3. 准备该 profile 的配置文件（独立）
4. 明确是否启用 heartbeat 以及周期（避免 9+ bot 同时心跳）

---

## 3. 实施步骤（推荐顺序）

## Step 0：基于来吉RVR模板复制（推荐）

可直接基于现有来吉RVR（reviewer-bot）做模板复制，降低从零配置风险。

建议流程：
1. 复制来吉RVR对应 profile 配置目录/文件
2. 仅修改“最小必要字段”（见第 8 节清单）
3. 启动前先做 schema 合法性检查

## Step 1：创建独立 profile 配置

- 新建 profile 对应配置（不要直接覆盖现有 profile）
- 填写 Discord token、agent 名称、mentionPatterns、模型等
- 若启用心跳，做错峰（与现有 bot 拉开间隔）

**关键检查**：
- 配置字段必须符合 schema（避免非法字段导致启动失败）
- mentionPatterns 与 bot 全称一致

## Step 2：启动新 profile

- 使用脚本重启/启动该 profile：
  - `~/.openclaw/scripts/gateway.sh restart <new-profile>`

**关键检查**：
- 进程是否存在
- 日志是否有 config invalid / auth failed / duplicate plugin 等错误

## Step 3：Discord 连通性验证

在 Discord 执行最小验证用例：
1. 发送 `@新Bot` 基础问候
2. 发送简单任务（单步）
3. 验证 bot 是否按预期回复
4. 验证不 @ 时是否保持安静（避免误触发）

## Step 4：协作链路验证

1. 由来财 `@新Bot` 派发一个明确任务
2. 验证新 bot 是否按规则 `@` 回执
3. 验证与其他 bot 的边界是否清晰（无重复响应）

## Step 5：稳定性观察（建议 24h）

- 观察心跳频率、响应延迟、错误日志
- 如出现 API 限流/403：立即调整 heartbeat 周期并错峰

---

## 4. 验收清单（上线门槛）

满足以下条件才算新增成功：

- [ ] Profile 可独立启动，且不影响其他 bot
- [ ] Discord token 生效，能收发消息
- [ ] mentionPatterns 触发准确，无误触发
- [ ] 最小任务可执行并回执
- [ ] 与现有 bot 协作无冲突
- [ ] 日志无持续报错（尤其配置与权限类）
- [ ] heartbeat 已错峰（如启用）

---

## 5. 常见故障与处理

### 5.1 `config invalid`
- 原因：配置字段不被 schema 支持
- 处理：先查 schema，再改配置；不要凭经验写字段

### 5.2 403 / 频繁限流
- 原因：多 bot 心跳或请求高峰重叠
- 处理：拉开心跳周期；减少无效轮询

### 5.3 duplicate plugin id
- 原因：内置插件与外装插件冲突
- 处理：卸载重复插件并重启 profile

### 5.4 @ 不触发或误触发
- 原因：mentionPatterns 配置与实际称呼不一致
- 处理：统一 bot 全称、触发词，并做回归测试

---

## 6. gateway.sh 与启动文件更新要点

在当前多 profile 体系下，新增 bot 通常需要补齐脚本管理入口：

1. 在 `~/.openclaw/scripts/gateway.sh` 中加入新 profile 的映射
   - 让 `start/stop/restart/status` 能识别该 profile
2. 若存在 profile 列表白名单（case/数组），加入新 profile 名称
3. 若脚本有日志路径或 pid 文件命名规则，确认新 profile 可正确落盘
4. 修改后先做 dry run/状态验证，确保不会影响既有 profile

> 原则：gateway.sh 只做“纳管入口扩展”，不改已有 profile 的启动参数语义。

## 7. 回滚方案

若新增 bot 异常影响系统：

1. 立即停止/重启该新 profile
2. 恢复其配置到上一个稳定版本
3. 保持其他 profile 不变，先确保存量 bot 正常
4. 记录故障原因后再二次尝试

---

## 8. 基于来吉RVR复制的新 Bot 最小变更清单

从来吉RVR模板复制后，至少需要检查并修改：

1. **身份与触发**
   - agent 名称（显示名）
   - mentionPatterns（触发词）
   - persona/SOUL/AGENTS 中角色描述

2. **Discord 接入**
   - bot token
   - app/client id（如配置使用）
   - 目标频道/权限范围（按最小权限）

3. **运行参数**
   - profile 名称（唯一）
   - 日志路径/pid 路径（避免与 reviewer-bot 冲突）
   - heartbeat 周期（错峰）

4. **记忆与目录**
   - memory 目录是否独立或符合当前团队策略
   - SESSION-STATE 与日记落盘路径是否正确

5. **规则与边界**
   - 删除 reviewer 专属规则（如评审专用约束）
   - 加入新角色职责边界，避免和现有 bot 重叠

---

## 9. 一句话结论

新增 Discord Bot 本身不难，真正关键是：**独立 profile、严格校验、错峰治理、可回滚上线**。做到这四点，系统可持续扩展。