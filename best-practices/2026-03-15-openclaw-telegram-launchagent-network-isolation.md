# OpenClaw Telegram 连接问题排查报告

**日期**: 2026-03-15
**问题**: OpenClaw Telegram Bot 无法连接，持续报 "Network request failed"
**影响时间**: 07:44 - 11:07 (约3小时23分钟)

---

## 问题现象

### 用户报告
- 早上 06:00-08:00 期间让 Telegram bot 处理了一些事情
- 配置 Gemini 搜索后，Telegram 就一直不响应
- 提示 "telegram 无法连接"
- 本地已开启 Clash Verge 代理（TUN 模式）
- 直接访问 Telegram 网页版正常
- 只有 OpenClaw 里面连接不上

### 日志表现
```
[telegram] deleteWebhook failed: Network request for 'deleteWebhook' failed!
[telegram] webhook cleanup failed: Network request for 'deleteWebhook' failed!; retrying in 30s.
[telegram] deleteMyCommands failed: Network request for 'deleteMyCommands' failed!
[telegram] setMyCommands failed: Network request for 'setMyCommands' failed!
[agent/embedded] embedded run agent end: isError=true error=LLM request timed out.
```

---

## 问题时间线

| 时间 | 事件 | 状态 |
|------|------|------|
| 07:00 之前 | 正常工作 | ✅ Telegram 和 AI 响应正常 |
| 07:41:41 | 配置 Gemini 搜索 | ✅ 配置热重载成功 |
| 07:42:35-37 | 发送消息 1723-1725 | ✅ Telegram 仍然正常 |
| 07:44:35 | **Gateway 被重启** | ⚠️ 收到 SIGTERM 信号 |
| 07:44:42 开始 | Telegram 连接失败 | ❌ 持续报 Network request failed |
| 10:57:29 | 配置代理后首次成功 | ✅ sendMessage ok |
| 11:07:05 | 使用环境变量启动 | ✅ 完全恢复正常 |

---

## 排查过程

### 1. 初步检查
- ✅ Telegram API 可访问（通过代理测试成功）
- ✅ Bot Token 有效
- ✅ 本地代理运行正常（Clash Verge on 127.0.0.1:7897）
- ❌ OpenClaw 无法连接 Telegram

### 2. 代理配置测试
```bash
# 测试代理访问 Telegram API
curl -x http://127.0.0.1:7897 https://api.telegram.org/bot.../getMe
# 结果：✅ 成功返回 bot 信息

# 配置 Telegram 代理
openclaw config set channels.telegram.proxy "http://127.0.0.1:7897"
# 结果：✅ Telegram 连接恢复
```

### 3. LLM 超时问题
- Telegram 连接恢复后，AI 仍然超时
- 原因：bytecat API 也需要代理
- 解决：通过环境变量为整个进程配置代理

### 4. TUN 模式深度排查

**检查 TUN 配置：**
```bash
# Clash Verge 配置
enable_tun_mode: true
device: utun1024
auto-route: true

# 路由表
198.18.0.1 -> utun1024 (正常)

# 测试普通进程
curl https://api.telegram.org/bot.../getMe
# 结果：✅ 成功（通过 TUN）

node -e "https.get('https://api.telegram.org/...')"
# 结果：✅ 成功（通过 TUN）
```

**关键发现：**
- ✅ TUN 模式正常工作
- ✅ 普通 curl 可以通过 TUN 访问 Telegram
- ✅ 普通 Node.js 进程可以通过 TUN 访问 Telegram
- ❌ **LaunchAgent 启动的 openclaw-gateway 无法通过 TUN**

### 5. LaunchAgent 环境检查
```xml
<!-- ~/Library/LaunchAgents/ai.openclaw.gateway.plist -->
<key>EnvironmentVariables</key>
<dict>
    <key>HOME</key>
    <string>/Users/mac</string>
    <key>TMPDIR</key>
    <string>/var/folders/.../T/</string>
</dict>
```

**问题所在：**
- LaunchAgent 只配置了 HOME 和 TMPDIR
- 没有代理相关的环境变量
- 运行在隔离的环境中，可能无法继承 TUN 路由

---

## 根本原因

### 不是 TUN 模式失效，而是 LaunchAgent 的网络隔离

**技术分析：**

1. **macOS LaunchAgent 的环境隔离**
   - LaunchAgent 启动的进程运行在独立的安全沙箱中
   - 不继承用户 shell 的环境变量和网络配置
   - 某些系统级网络配置（如 TUN 路由）可能对其不生效

2. **为什么之前能工作？**
   - 可能之前的进程是通过用户 shell 启动的（继承了完整环境）
   - 07:44 的重启触发了 LaunchAgent 接管
   - LaunchAgent 启动的进程失去了 TUN 路由能力

3. **为什么配置代理后就好了？**
   - 显式配置 HTTP 代理绕过了系统路由
   - 代理服务器在本地（127.0.0.1:7897），不受 TUN 影响
   - 通过 HTTP CONNECT 隧道直接连接目标服务器

---

## 解决方案

### 最终方案：管理脚本 + 环境变量

创建了 `~/.openclaw/start-gateway.sh` 管理脚本：

```bash
#!/bin/bash
# OpenClaw Gateway 管理脚本（带代理支持）

PROXY_URL="http://127.0.0.1:7897"
PID_FILE="$HOME/.openclaw/gateway.pid"
LOG_FILE="$HOME/.openclaw/logs/gateway-proxy.log"

# 设置代理环境变量
set_proxy() {
    export HTTPS_PROXY="$PROXY_URL"
    export HTTP_PROXY="$PROXY_URL"
    export https_proxy="$PROXY_URL"
    export http_proxy="$PROXY_URL"
}

# 启动/停止/重启/状态/日志 功能...
```

**使用方法：**
```bash
~/.openclaw/start-gateway.sh start    # 启动
~/.openclaw/start-gateway.sh stop     # 停止
~/.openclaw/start-gateway.sh restart  # 重启
~/.openclaw/start-gateway.sh status   # 状态
~/.openclaw/start-gateway.sh logs     # 日志
```

**配置变更：**
```json
// ~/.openclaw/openclaw.json
{
  "channels": {
    "telegram": {
      "proxy": "http://127.0.0.1:7897"  // 添加此行
    }
  }
}
```

---

## 技术要点

### 1. OpenClaw 代理配置层级

| 层级 | 配置方式 | 适用范围 | 优先级 |
|------|---------|---------|--------|
| 进程环境变量 | `HTTP_PROXY=...` | 所有 HTTP/HTTPS 请求 | 低 |
| Channel 配置 | `channels.telegram.proxy` | Telegram 连接 | 高 |
| Provider 配置 | `models.providers.*.proxy` | ❌ 不支持 | - |

### 2. macOS 网络栈特性

- **TUN 模式**：虚拟网络接口，透明代理所有流量
- **LaunchAgent 隔离**：系统服务运行在受限环境中
- **路由继承问题**：LaunchAgent 进程可能无法继承用户级路由配置

### 3. Node.js 代理支持

```javascript
// Node.js 自动识别的环境变量
process.env.HTTP_PROXY
process.env.HTTPS_PROXY
process.env.http_proxy
process.env.https_proxy
process.env.ALL_PROXY
process.env.all_proxy
```

---

## 经验教训

### 1. 诊断思路
- ✅ 从现象到原因：连接失败 → 代理问题 → 环境隔离
- ✅ 逐层测试：curl → node → openclaw
- ✅ 对比分析：普通进程 vs LaunchAgent 进程

### 2. 常见误区
- ❌ "TUN 模式开了就应该全局生效" → 不适用于 LaunchAgent
- ❌ "配置文件改了就应该生效" → 需要重启进程
- ❌ "代理只需要配置一个地方" → 需要多层配置

### 3. 最佳实践
- ✅ 关键服务使用显式代理配置，不依赖透明代理
- ✅ LaunchAgent 服务需要在 plist 中配置环境变量
- ✅ 提供管理脚本统一启动方式，确保环境一致

---

## 附录

### A. 相关配置文件

**OpenClaw 配置：** `~/.openclaw/openclaw.json`
```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "botToken": "8733691728:AAF...",
      "proxy": "http://127.0.0.1:7897"
    }
  },
  "models": {
    "providers": {
      "bytecat": {
        "baseUrl": "https://bytecat.lamclod.cn",
        "apiKey": "sk-..."
      }
    }
  }
}
```

**Clash Verge 配置：** `~/Library/Application Support/io.github.clash-verge-rev.clash-verge-rev/config.yaml`
```yaml
mixed-port: 7897
tun:
  enable: true
  device: utun1024
  auto-route: true
  dns-hijack:
    - any:53
```

### B. 关键日志片段

**问题发生前（正常）：**
```
2026-03-15T07:42:35.844+08:00 [telegram] sendMessage ok chat=7872580401 message=1723
2026-03-15T07:42:36.868+08:00 [telegram] sendMessage ok chat=7872580401 message=1724
```

**问题发生后（失败）：**
```
2026-03-15T07:44:42.225+08:00 [telegram] deleteWebhook failed: Network request for 'deleteWebhook' failed!
2026-03-15T07:44:44.623+08:00 [telegram] deleteWebhook failed: Network request for 'deleteWebhook' failed!
```

**修复后（恢复）：**
```
2026-03-15T10:57:29.598+08:00 [telegram] sendMessage ok chat=7872580401 message=1730
2026-03-15T11:07:05.708+08:00 [gateway] listening on ws://127.0.0.1:18789 (PID 58680)
```

### C. 测试命令

```bash
# 测试 TUN 模式
curl https://api.telegram.org/bot.../getMe

# 测试代理
curl -x http://127.0.0.1:7897 https://api.telegram.org/bot.../getMe

# 测试 Node.js
node -e "const https = require('https'); https.get('https://api.telegram.org/bot.../getMe', ...)"

# 检查路由
netstat -rn | grep 198.18

# 检查 TUN 接口
ifconfig utun1024

# 检查进程环境变量
ps eww -p <PID> | tr '\0' '\n' | grep -i proxy
```

---

## 总结

这是一个典型的 **macOS LaunchAgent 网络隔离问题**，而非 TUN 模式失效。通过配置显式代理和使用管理脚本确保环境变量正确传递，问题得到彻底解决。

**关键要点：**
1. LaunchAgent 进程运行在隔离环境中，不继承用户网络配置
2. TUN 模式对普通进程有效，但对 LaunchAgent 可能无效
3. 显式配置代理是更可靠的解决方案
4. 环境变量需要在进程启动时正确设置

**后续建议：**
- 考虑将代理配置写入 LaunchAgent plist 的 EnvironmentVariables
- 或者完全使用管理脚本启动，不依赖 LaunchAgent
- 监控日志，及时发现类似问题

---

**报告生成时间**: 2026-03-15 11:20
**问题解决时间**: 2026-03-15 11:07
**总耗时**: 约3小时23分钟
