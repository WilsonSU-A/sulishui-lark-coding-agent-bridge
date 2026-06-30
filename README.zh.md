# sulishui-lark-agent

> 把飞书 / Lark 变成 Claude Code 和 Codex CLI 的任务入口。

支持 **Claude Code + Codex CLI**，支持 **macOS + Windows + Linux**，支持飞书流式卡片、消息收发、reaction、文件/图片、多工作空间、后台服务。

项目地址：<https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge>

---

## 最简单安装方式

不要自己逐条敲命令，也不要手动复制 App ID / App Secret。

直接把下面这段话发给 **Claude Code** 或 **Codex**：

```text
请帮我安装并配置这个飞书/Lark 编程助手桥接项目：

https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge

目标：
1. 在我的电脑上安装 sulishui-lark-agent。
2. 自动检查并修复缺失环境，包括 Node.js >= 20.12、npm/corepack/pnpm、git，以及本机已有的 claude 或 codex CLI。
3. 优先使用 npm 从 GitHub 安装：
   npm install -g github:WilsonSU-A/sulishui-lark-coding-agent-bridge
4. 如果 GitHub 全局安装失败，就自动 clone 仓库、安装依赖、build，并用 npm link 或等效方式完成本地安装。
5. 不要覆盖我已有的 ~/.lark-channel 配置；如果已有 claude/codex profile，要保留并复用。
6. 安装后运行 sulishui-lark-agent --version 验证命令可用。
7. 使用 sulishui-lark-agent 的首次初始化/授权流程来绑定飞书 PersonalAgent。不要让我手动输入或粘贴 App ID、App Secret，也不要把密钥写进聊天记录。
8. 如果需要授权，直接打开飞书/Lark 的授权链接或显示官方二维码，让我在飞书页面里选择智能体并确认权限。
9. 授权完成后启动后台服务：
   sulishui-lark-agent start --profile codex --agent codex
   或按我的选择启动 claude profile。
10. 最后运行 sulishui-lark-agent status --profile codex 验证飞书 bot 已经在线。

请全程自动处理。只有在需要我点击飞书授权链接、扫码、选择智能体、确认权限、登录 GitHub/飞书，或批准管理员权限时再停下来问我。
```

复制这段话给 Agent 后，它会自己完成环境检查、安装、授权引导、启动和验证。

---

## 安全绑定流程

推荐流程是 **链接跳转 / 官方二维码授权**：

1. Agent 启动 `sulishui-lark-agent run` 或初始化流程。
2. 终端显示飞书/Lark 授权链接或官方二维码。
3. 用户在飞书页面里选择要绑定的 PersonalAgent 智能体。
4. 用户在飞书页面确认所需权限。
5. bridge 自动保存授权结果到本地加密配置。
6. Agent 启动后台服务并执行 `status` 验证。

安全原则：

- 不要求用户手动复制 App ID。
- 不要求用户手动复制 App Secret。
- 不在聊天记录里暴露密钥。
- 不覆盖已有 `~/.lark-channel` 配置。
- 如果已有 profile，优先复用并保留。

---

## 这个项目解决什么问题

`sulishui-lark-agent` 是一个本地桥接服务。它把飞书 / Lark 消息转发给你电脑上的 Claude Code 或 Codex CLI，再把 agent 的回复以飞书卡片形式发回去。

你可以在飞书里：

- 私聊 bot，让 Codex 或 Claude Code 帮你改代码、查项目、跑命令。
- 在群里 @bot，把任务丢给本机 agent。
- 发送图片和文件，让 agent 在本地处理。
- 用 `/status`、`/new`、`/cd`、`/ws`、`/stop` 管理会话和工作目录。

---

## 和上游项目有什么不同

本项目基于 [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) 构建，并保持上游能力兼容。

| 能力 | sulishui-lark-agent |
|---|---|
| Claude Code | 支持 |
| Codex CLI | 支持 |
| macOS | launchd 后台服务 |
| Windows | Task Scheduler 后台服务 |
| Linux | systemd 后台服务 |
| 新命令 | `sulishui-lark-agent` |
| 旧命令兼容 | `lark-channel-bridge` 仍可用 |
| 配置兼容 | 继续使用 `~/.lark-channel`，已有 profile 无需迁移 |
| 推荐绑定方式 | 飞书授权链接 / 官方二维码，不手动输入密钥 |

---

## 手动命令（备用）

日常用户不需要看这一节。只有调试时才建议手动运行：

```bash
sulishui-lark-agent run
sulishui-lark-agent start --profile codex --agent codex
sulishui-lark-agent start --profile claude --agent claude
sulishui-lark-agent status --profile codex
sulishui-lark-agent stop --profile codex
```

---

## License

MIT
