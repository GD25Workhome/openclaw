# Mac 安装 OpenClaw 调研

> 文档日期：2025-03-02  
> 用途：在 macOS 上安装并运行 OpenClaw（自托管 AI 助手）的调研与步骤汇总。

---

## 1. OpenClaw 简介

OpenClaw 是一款可自托管的 AI 助手平台，支持通过 Gateway 连接多种渠道（如 Telegram、Discord、WhatsApp 等），并可与 Claude、OpenAI、Google Gemini 等大模型配合使用。

---

## 2. 系统与前置要求

### 2.1 系统要求

| 项目     | 要求说明                                     |
| -------- | -------------------------------------------- |
| 操作系统 | macOS Monterey (12.0) 或更高，推荐 macOS 13+ |
| 架构     | Intel 或 Apple Silicon (M1/M2/M3) 均支持     |
| 内存     | 至少 4GB，推荐 8GB 及以上                    |
| 磁盘空间 | 至少约 500MB 可用空间                        |

### 2.2 必装依赖

- **Node.js**：**22.0.0 或更高**（安装前请用 `node --version` 确认）
- **pnpm**：若从源码构建或使用“开发工作流”时需要
- **Xcode**：仅当从源码构建 **macOS 原生 App** 时需要（文档要求 Xcode 26.2+、Swift 6.2，实际以官方文档为准）

---

## 3. 安装方式概览

Mac 上主要有三种使用方式：

1. **推荐：官方一键脚本 / npm 全局安装** — 只跑 CLI + Gateway，最快上手。
2. **使用官方 macOS 应用** — 带图形界面，由应用管理 Gateway。
3. **从源码构建 macOS App** — 适合参与开发或需要改代码。

下面分别说明。

---

## 4. 方式一：推荐安装（CLI + Gateway）

适合：只想在 Mac 上跑起来 OpenClaw、用浏览器或渠道聊天。

### 4.1 安装 Node.js 22+

若未安装或版本低于 22：

- **Homebrew（推荐）**：`brew install node`
- **nvm**：`nvm install 22 && nvm use 22`
- 或从 [nodejs.org](https://nodejs.org) 下载安装

Apple Silicon 建议使用 ARM64 版本 Node.js，避免兼容问题。

### 4.2 安装 OpenClaw

任选其一即可。

**方法 A：一键脚本（推荐）**

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**方法 B：npm 全局安装**

```bash
npm install -g openclaw@latest
```

若出现 `command not found`，可尝试重启终端，或：

```bash
sudo npm install -g openclaw@latest
```

### 4.3 运行引导向导并安装 Daemon

```bash
openclaw onboard --install-daemon
```

向导会引导配置：

- 网关（Gateway）与 Daemon
- 技能（Skills）
- 渠道（Telegram、Discord 等）
- 大模型 API 密钥（如 Anthropic、OpenAI、Google 等）

### 4.4 启动服务并验证

```bash
# 启动 daemon（若向导中已选安装 daemon，通常已自动启动）
openclaw daemon start

# 查看网关状态
openclaw gateway status

# 健康检查
openclaw health
```

### 4.5 使用方式

- **浏览器控制台**：`openclaw dashboard`，或在浏览器打开 `http://127.0.0.1:18789/`
- **命令行聊天**：`openclaw chat`

---

## 5. 方式二：使用 macOS 官方应用

若希望用菜单栏/图形界面管理 OpenClaw，可安装官方 macOS 应用。

- 应用会**管理 Gateway 生命周期**（本地模式时由应用启动/停止 Gateway）。
- 需先安装 **OpenClaw CLI**（同上：脚本或 `npm install -g openclaw@latest`），应用内可通过「Install CLI」或设置页完成。
- 首次运行按提示完成 **Onboarding** 和系统权限（如麦克风、语音识别等 TCC 提示）。

典型流程：

1. 安装 CLI（见方式一）。
2. 安装并打开 OpenClaw macOS 应用。
3. 在应用内完成引导、权限与渠道登录，例如：
   ```bash
   openclaw channels login
   ```
4. 连接模式选 **Local**，由应用托管 Gateway。

---

## 6. 方式三：从源码构建 macOS App（开发者）

仅当需要修改或调试 macOS 应用或 Gateway 源码时使用。

### 6.1 前置

- Node.js 22+、pnpm
- Xcode（文档写 26.2+、Swift 6.2，以 [macOS Dev Setup](https://docs.openclaw.ai/platforms/mac/dev-setup) 为准）

### 6.2 克隆与构建

```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw
pnpm install
./scripts/package-mac-app.sh
```

产物在 `dist/OpenClaw.app`。无 Apple 开发者证书时脚本会使用 ad-hoc 签名（`-`），可能遇到安全提示或“Abort trap 6”等问题，需参考官方 [macOS README](https://github.com/openclaw/openclaw/blob/main/apps/macos/README.md) 与 [Troubleshooting](https://docs.openclaw.ai/platforms/mac/dev-setup#troubleshooting)。

### 6.3 CLI 与 Gateway

- 应用期望本机已安装全局 `openclaw` CLI（同上：`npm install -g openclaw@<version>`）。
- 也可在仓库内用 `pnpm gateway:watch` 跑开发版 Gateway，应用选 **Local** 连接。

---

## 7. 配置与数据路径（便于排查与备份）

| 用途         | 路径                               |
| ------------ | ---------------------------------- |
| 配置         | `~/.openclaw/openclaw.json`        |
| 工作区定制   | `~/.openclaw/workspace/`           |
| 旧版配置目录 | `~/.config/openclaw/`              |
| 凭证等       | `~/.openclaw/credentials/`         |
| 会话         | `~/.openclaw/agents/.../sessions/` |
| 日志         | `/tmp/openclaw/`                   |

建议将 `~/.openclaw/workspace` 和 `~/.openclaw/openclaw.json` 单独备份或纳入版本管理，便于换机或重装后恢复。

---

## 8. 常见问题与排查

### 8.1 命令找不到（command not found）

- 确认 Node 版本：`node --version`（≥22）。
- 重启终端；必要时用 `sudo npm install -g openclaw@latest` 重装。

### 8.2 Gateway 一直显示 “Starting...”

- 检查端口占用（默认 18789）：
  ```bash
  openclaw gateway status
  openclaw gateway stop
  lsof -nP -iTCP:18789 -sTCP:LISTEN
  ```
- 若有其他进程占用，结束该进程或改用其他端口。

### 8.3 macOS 应用在授权时崩溃

- 可能与 TCC 权限缓存或签名有关，可尝试重置权限：
  ```bash
  tccutil reset All ai.openclaw.mac.debug
  ```
- 仍异常时参考官方 [Troubleshooting](https://docs.openclaw.ai/platforms/mac/dev-setup#troubleshooting)（如临时修改 `BUNDLE_ID` 等）。

### 8.4 环境变量（可选）

- `OPENCLAW_CONFIG_PATH`：覆盖配置文件路径
- `OPENCLAW_STATE_DIR`：覆盖状态目录
- `OPENCLAW_HOME`：内部路径解析的“主目录”

---

## 9. 参考链接

- 官方安装说明：[Getting Started](https://docs.openclaw.ai/start/getting-started) / [Setup](https://docs.openclaw.ai/setup)
- Mac 开发/应用构建：[macOS Dev Setup](https://docs.openclaw.ai/platforms/mac/dev-setup)
- 仓库：[github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
- 一键安装脚本：`https://openclaw.ai/install.sh`（macOS/Linux）

---

## 10. 小结

- **仅想在本机使用**：用 **方式一**（`curl ... install.sh \| bash` 或 `npm install -g openclaw@latest` → `openclaw onboard --install-daemon`）即可。
- **想要图形界面**：在方式一的基础上安装并配置 **macOS 应用**（方式二）。
- **要改代码或调试**：再按 **方式三** 克隆仓库并构建 macOS App，并配合 `pnpm gateway:watch` 使用。

整体首次配置约 15–20 分钟，主要时间在 Onboarding 与渠道、API 配置。
