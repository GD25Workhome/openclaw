# OpenClaw 启动、停用与状态检查说明

> 文档日期：2025-03-02  
> 用途：汇总 OpenClaw Gateway/Daemon 的启动、停止与状态检查命令，便于日常使用与排查。

---

## 1. 检查状态

在启动或停用前后，可先查看当前运行情况：

```bash
openclaw gateway status
```

- 若显示网关已运行、端口正常，说明当前可用。
- 若显示已停止或未运行，需要执行启动命令后再使用。

---

## 2. 启动方式

### 2.1 后台运行（推荐）

若安装时执行过 `openclaw onboard --install-daemon`，可使用 daemon 在后台启动：

```bash
openclaw daemon start
```

- Gateway 在后台持续运行，关闭终端也不会退出。
- 适合日常长期使用。

### 2.2 前台运行（临时/调试）

不经过 daemon，在当前终端直接启动网关：

```bash
openclaw gateway --port 18789
```

- 默认端口为 `18789`，一般无需修改。
- 关闭该终端或按 Ctrl+C 即停止网关。
- 适合临时测试或排查问题。

---

## 3. 停用方式

### 3.1 停止网关（常用）

```bash
openclaw gateway stop
```

或若使用 daemon 管理时：

```bash
openclaw daemon stop
```

- 执行后网页控制台、飞书/Telegram 等渠道将无法连接，直到再次启动。

### 3.2 确认已停止

停用后再次检查：

```bash
openclaw gateway status
```

确认状态为已停止即可。

### 3.3 关闭开机自启（macOS）

若通过 `openclaw onboard --install-daemon` 安装了 LaunchAgent，重启后可能会自动启动。若要彻底停用、下次开机也不自动运行：

1. **若安装了 OpenClaw macOS 应用**：在应用设置中关闭「开机启动」或「后台服务」等选项。
2. **命令行方式**：查看是否有 OpenClaw 的 LaunchAgent：
   ```bash
   ls ~/Library/LaunchAgents/ | grep -i openclaw
   ```
   若有对应 `.plist` 文件，可卸载（将 `xxx.openclaw.xxx.plist` 替换为实际文件名）：
   ```bash
   launchctl unload ~/Library/LaunchAgents/xxx.openclaw.xxx.plist
   ```

---

## 4. 常用命令速查

| 操作           | 命令                            |
| -------------- | ------------------------------- |
| 检查状态       | `openclaw gateway status`       |
| 后台启动       | `openclaw daemon start`         |
| 前台启动       | `openclaw gateway --port 18789` |
| 停止网关       | `openclaw gateway stop`         |
| 停止 daemon    | `openclaw daemon stop`          |
| 打开网页控制台 | `openclaw dashboard`            |
| 终端聊天       | `openclaw chat`                 |

---

## 5. 相关文档

- 安装与整体配置见：[030201-Mac安装OpenClaw调研.md](./030201-Mac安装OpenClaw调研.md)
- 官方文档：<https://docs.openclaw.ai/>
