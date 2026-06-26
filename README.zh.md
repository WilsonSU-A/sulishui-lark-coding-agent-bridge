# sulishui-lark-agent

> 本地编程助手 × 飞书 IM 的跨平台桥接层

**Claude Code + Codex CLI，macOS + Windows + Linux，一个命令全搞定。**

扫码绑定飞书应用，私聊或群 @ 就能对接到本机的 Claude Code 或 Codex CLI。流式卡片实时渲染、会话隔离、图片文件直传、多工作空间切换、交互卡片按钮。支持后台服务常驻。

> 基于 [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) 构建并独立演进。兼容 `lark-channel-bridge` 旧命令，已有 profile 无需迁移。

---

## 为什么用这个

| | sulishui-lark-agent | 上游 lark-channel-bridge |
|---|---|---|
| Claude Code | 一等支持 | 一等支持 |
| Codex CLI | 一等支持 | 一等支持 |
| macOS 后台服务 | launchd | launchd |
| Windows 后台服务 | Task Scheduler | Task Scheduler |
| Linux 后台服务 | systemd | systemd |
| 独立品牌 CLI | `sulishui-lark-agent` | `lark-channel-bridge` |
| 旧命令兼容 | `lark-channel-bridge` 仍可用 | - |
| 品牌服务名 | `sulishui-lark-agent.bot` | `lark-channel-bridge.bot` |

**一句话：上下游能力对齐，但品牌独立、跨平台完整、旧命令无缝兼容。**

---

## 前置条件

- Node.js >= 20.12.0
- 至少一个本地 agent 已安装并登录：`claude` 或 `codex`
- 一个飞书 PersonalAgent 应用（首次扫码自动创建）

## 安装

```bash
npm i -g sulishui-lark-coding-agent-bridge
```

## 快速开始

```bash
sulishui-lark-agent run
```

终端渲染二维码 → 飞书扫码 → 选择 agent → 开始对话。

```bash
# 已有应用直接指定
sulishui-lark-agent run --app-id cli_xxx

# 直接启动后台常驻
sulishui-lark-agent start --app-id cli_xxx
```

---

## 同时跑 Claude + Codex

```bash
sulishui-lark-agent start --profile claude --agent claude
sulishui-lark-agent start --profile codex --agent codex
```

每个 profile 独立的凭据、会话、工作目录、日志，互不干扰。

---

## 后台服务

```bash
sulishui-lark-agent start      # 安装并启动系统服务
sulishui-lark-agent status     # 查看运行状态
sulishui-lark-agent stop       # 停止
sulishui-lark-agent restart    # 重启
sulishui-lark-agent unregister # 完全移除
```

| 平台 | 服务类型 | 服务名 |
|------|---------|--------|
| macOS | launchd | `ai.sulishui-lark-agent.bot.<profile>` |
| Windows | Task Scheduler | `SulishuiLarkAgent.Bot.<profile>` |
| Linux | systemd | `sulishui-lark-agent.bot.<profile>.service` |

---

## 主要功能

- **流式卡片** — 回复和工具调用在同一张飞书卡片上实时更新
- **会话延续** — 每个聊天/话题/文档评论独立会话，互不串线
- **消息合并** — 短时间连续消息自动合并；`/new` `/cd` `/ws` `/stop` 可中断当前任务
- **多工作空间** — `/cd` 切换项目，`/ws` 保存常用目录
- **图片/文件** — 直接发给 bot，bridge 下载到本地后交给 agent
- **交互卡片** — `/help` `/ws list` `/status` 返回可点击按钮卡片
- **权限管理** — `/invite` `/remove` `/config` 精细控制谁能用

---

## CLI 命令参考

```
sulishui-lark-agent run       前台运行
sulishui-lark-agent start     后台服务
sulishui-lark-agent stop      停止服务
sulishui-lark-agent restart   重启服务
sulishui-lark-agent status    查看状态
sulishui-lark-agent ps        列出本机所有运行中的 bot
sulishui-lark-agent kill <id> 终止指定 bot
```

### Profile 管理

```bash
sulishui-lark-agent profile create <name> --agent claude|codex
sulishui-lark-agent profile list
sulishui-lark-agent profile use <name>
sulishui-lark-agent profile remove <name>
sulishui-lark-agent profile export <name>
```

---

## 环境变量

| 变量 | 说明 |
|------|------|
| `LARK_CHANNEL_HOME` | 配置根目录，默认 `~/.lark-channel` |
| `LARK_CHANNEL_CODEX_BIN` | Codex CLI 路径，默认 `codex` |

---

## 文件结构

| 路径 | 内容 |
|------|------|
| `~/.lark-channel/config.json` | profiles 和 active profile |
| `~/.lark-channel/profiles/<p>/sessions.json` | 会话状态 |
| `~/.lark-channel/profiles/<p>/workspaces.json` | 工作空间绑定 |
| `~/.lark-channel/profiles/<p>/secrets.enc` | 加密凭据 |
| `~/.lark-channel/profiles/<p>/logs/` | 日志 |

---

## 回退到上游

```bash
pnpm add -g lark-channel-bridge
# 旧命令仍然兼容
lark-channel-bridge run --profile codex
```

## 从上游同步

```bash
git fetch upstream
git merge upstream/main
pnpm build
```

## License

MIT
