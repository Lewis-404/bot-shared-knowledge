# 远程服务器 OpenClaw 代理接入执行流程（复用版）

> 版本：v1.0  
> 生成时间：2026-03-24  
> 适用场景：Linux 远程主机上 OpenClaw 能启动但 Discord 离线（日志 `fetch failed`）

---

## 1. 目标

在**不改系统全局网络路由**的前提下，只让 OpenClaw 进程走代理，恢复 Discord 连接。

---

## 2. 前提条件

- 远程服务器可 SSH 登录（示例：`dx@192.168.88.103`）
- OpenClaw 三实例（或多实例）已可正常启动（systemd user）
- 代理上游可用（本方案示例用 `socks5 192.168.99.80 7891`）
- 已安装 `proxychains4`

安装（Ubuntu/Debian）：

```bash
sudo apt-get update
sudo apt-get install -y proxychains4
```

---

## 3. 典型故障特征（先确认）

先确认是“网络出站被拦”而不是 OpenClaw 本身挂了：

```bash
openclaw gateway status
systemctl --user status openclaw-gateway.service --no-pager
journalctl --user -u openclaw-gateway.service -n 80 --no-pager
```

如果出现以下组合，基本就是同类问题：
- service `active (running)`
- 日志有：`Failed to get gateway information from Discord: fetch failed`
- 或：`failed to fetch bot identity: TypeError: fetch failed`

---

## 4. 实施方案（推荐：proxychains 注入 ExecStart）

> 思路：不走环境变量 `HTTP_PROXY/HTTPS_PROXY`，改为 systemd unit 的 `ExecStart` 包一层 `proxychains4`。

### Step 1）创建 proxychains 配置

```bash
mkdir -p ~/.config/proxychains
cat > ~/.config/proxychains/openclaw.conf <<'EOF'
strict_chain
proxy_dns
remote_dns_subnet 224
tcp_read_time_out 15000
tcp_connect_time_out 8000

[ProxyList]
socks5 192.168.99.80 7891
EOF
```

建议补充 localnet 直连（如你内网依赖较多）：

```bash
cat >> ~/.config/proxychains/openclaw.conf <<'EOF'

localnet 127.0.0.0/255.0.0.0
localnet ::1/128
EOF
```

---

### Step 2）为每个 OpenClaw 服务写 drop-in（覆盖 ExecStart）

以三个服务为例：
- `openclaw-gateway.service`
- `openclaw-gateway-frontend-bot.service`
- `openclaw-gateway-backend-bot.service`

先看原始启动命令：

```bash
systemctl --user cat openclaw-gateway.service
```

然后创建 drop-in（关键：先 `ExecStart=` 清空，再写新命令）：

```bash
mkdir -p ~/.config/systemd/user/openclaw-gateway.service.d
cat > ~/.config/systemd/user/openclaw-gateway.service.d/override.conf <<'EOF'
[Service]
ExecStart=
ExecStart=/usr/bin/proxychains4 -f /home/dx/.config/proxychains/openclaw.conf /usr/bin/node /home/dx/.nvm/versions/node/v25.6.1/bin/openclaw gateway run --config /home/dx/.openclaw/openclaw.json --agent gateway
EOF
```

frontend / backend 同理，替换为各自原始 node 启动命令。

> 注意：`/usr/bin/node`、`openclaw` 路径、`--config` 路径请按你的机器实际值替换。

---

### Step 3）重载并重启

```bash
systemctl --user daemon-reload
~/.openclaw/scripts/gateway.sh restart
```

如果你是多 profile 管理，也可单独重启：

```bash
~/.openclaw/scripts/gateway.sh restart gateway
~/.openclaw/scripts/gateway.sh restart frontend-bot
~/.openclaw/scripts/gateway.sh restart backend-bot
```

---

## 5. 验收（必须执行）

### 5.1 服务状态

```bash
systemctl --user is-active openclaw-gateway.service
systemctl --user is-active openclaw-gateway-frontend-bot.service
systemctl --user is-active openclaw-gateway-backend-bot.service
```

应全部返回 `active`。

### 5.2 确认 ExecStart 已注入 proxychains

```bash
systemctl --user show openclaw-gateway.service -p ExecStart
```

输出中应包含：`proxychains4 -f ... openclaw.conf`。

### 5.3 代理链路测试

```bash
proxychains4 -f ~/.config/proxychains/openclaw.conf curl -I https://discord.com/api/v10/gateway
```

期望看到 `HTTP/2 200`（或等价成功响应）。

### 5.4 业务日志确认

```bash
journalctl --user -u openclaw-gateway.service -n 120 --no-pager
```

期望出现：`logged in to discord as ...`。

---

## 6. 回滚方案（1 分钟恢复）

如果要取消代理注入：

```bash
rm -f ~/.config/systemd/user/openclaw-gateway.service.d/override.conf
rm -f ~/.config/systemd/user/openclaw-gateway-frontend-bot.service.d/override.conf
rm -f ~/.config/systemd/user/openclaw-gateway-backend-bot.service.d/override.conf

systemctl --user daemon-reload
~/.openclaw/scripts/gateway.sh restart
```

回滚后会恢复 unit 原始 `ExecStart`。

---

## 7. 常见坑位

1. **只配 HTTP_PROXY/HTTPS_PROXY 但本机无 7890 监听** → 看似配置了代理，实际没流量可走。  
2. **drop-in 忘了 `ExecStart=` 清空** → systemd 叠加失败或启动异常。  
3. **写死了错误的 node/openclaw 路径** → 服务起不来。  
4. **重启后不验收日志** → 误判“已修复”。

---

## 8. 建议复用模板（给新机器）

迁移到另一台电脑时，建议只替换 4 个变量：

- `HOST_USER_HOME`（如 `/home/dx`）
- `NODE_BIN`（如 `/usr/bin/node` 或 nvm 路径）
- `OPENCLAW_BIN`（openclaw 可执行路径）
- `SOCKS_UPSTREAM`（如 `192.168.99.80:7891`）

其余流程保持不变：

> 诊断 → 写 proxychains 配置 → 写 drop-in 覆盖 ExecStart → daemon-reload + restart → 4 项验收。

---

## 9. 一句话结论

这套方案的核心价值是：**只改 OpenClaw 进程，不碰系统全局代理**，风险小、可回滚、可快速复制到新机器。
