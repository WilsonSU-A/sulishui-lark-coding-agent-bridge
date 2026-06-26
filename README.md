# sulishui-lark-agent

> Local coding agents meet Feishu/Lark IM — cross-platform

**Claude Code + Codex CLI · macOS + Windows + Linux · one command, zero friction.**

Bridge your local Claude Code or Codex CLI into Feishu/Lark. Scan a QR, bind a bot, and talk to your agent in chat. Streaming cards, session isolation, file attachments, multi-workspace, interactive buttons. Background daemon on all three platforms.

> Built on [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) with independent evolution. Legacy `lark-channel-bridge` command still works — no migration needed.

[中文文档](./README.zh.md)

---

## Why this fork

| | sulishui-lark-agent | upstream lark-channel-bridge |
|---|---|---|
| Claude Code | first-class | first-class |
| Codex CLI | first-class | first-class |
| macOS daemon | launchd | launchd |
| Windows daemon | Task Scheduler | Task Scheduler |
| Linux daemon | systemd | systemd |
| CLI name | `sulishui-lark-agent` | `lark-channel-bridge` |
| Legacy alias | `lark-channel-bridge` | - |
| Service name | `sulishui-lark-agent.bot` | `lark-channel-bridge.bot` |

**Feature parity with upstream, plus independent branding and seamless legacy compatibility.**

---

## Prerequisites

- Node.js >= 20.12.0
- At least one local agent installed and authenticated: `claude` or `codex`
- A Feishu/Lark PersonalAgent app (auto-created on first run)

## Install

```bash
npm i -g sulishui-lark-coding-agent-bridge
```

## Quick Start

```bash
sulishui-lark-agent run
```

Terminal QR → scan with Feishu → pick agent → start chatting.

```bash
# Skip QR with existing app credentials
sulishui-lark-agent run --app-id cli_xxx

# Bootstrap directly into background daemon
sulishui-lark-agent start --app-id cli_xxx
```

---

## Run Claude + Codex Side by Side

```bash
sulishui-lark-agent start --profile claude --agent claude
sulishui-lark-agent start --profile codex --agent codex
```

Each profile gets its own credentials, sessions, workspaces, and logs.

---

## Background Service

```bash
sulishui-lark-agent start      # install & start OS-managed daemon
sulishui-lark-agent status     # check running state
sulishui-lark-agent stop       # stop daemon
sulishui-lark-agent restart    # restart
sulishui-lark-agent unregister # full cleanup
```

| Platform | Service Backend | Service ID |
|----------|----------------|------------|
| macOS | launchd | `ai.sulishui-lark-agent.bot.<profile>` |
| Windows | Task Scheduler | `SulishuiLarkAgent.Bot.<profile>` |
| Linux | systemd | `sulishui-lark-agent.bot.<profile>.service` |

---

## Features

- **Streaming cards** — replies and tool calls render live on a single Lark card
- **Session isolation** — each chat, topic, or doc comment keeps its own session
- **Message batching** — rapid-fire messages merge; `/new` `/cd` `/ws` `/stop` interrupt mid-run
- **Multi-workspace** — `/cd` to switch projects, `/ws` to save directories
- **Attachments** — send images/files directly; bridge downloads them locally
- **Interactive cards** — `/help` `/ws list` `/status` return clickable buttons
- **Access control** — `/invite` `/remove` `/config` for fine-grained permissions

---

## CLI Reference

```
sulishui-lark-agent run       foreground process
sulishui-lark-agent start     background daemon
sulishui-lark-agent stop      stop daemon
sulishui-lark-agent restart   restart daemon
sulishui-lark-agent status    daemon status
sulishui-lark-agent ps        list all running bots
sulishui-lark-agent kill <id> terminate a bot
```

### Profile Management

```bash
sulishui-lark-agent profile create <name> --agent claude|codex
sulishui-lark-agent profile list
sulishui-lark-agent profile use <name>
sulishui-lark-agent profile remove <name>
sulishui-lark-agent profile export <name>
```

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `LARK_CHANNEL_HOME` | Config root, default `~/.lark-channel` |
| `LARK_CHANNEL_CODEX_BIN` | Codex CLI binary path, default `codex` |

---

## File Layout

| Path | Content |
|------|---------|
| `~/.lark-channel/config.json` | profiles and active profile |
| `~/.lark-channel/profiles/<p>/sessions.json` | session state |
| `~/.lark-channel/profiles/<p>/workspaces.json` | workspace bindings |
| `~/.lark-channel/profiles/<p>/secrets.enc` | encrypted credentials |
| `~/.lark-channel/profiles/<p>/logs/` | log files |

---

## Rollback to Upstream

```bash
pnpm add -g lark-channel-bridge
# legacy command still works
lark-channel-bridge run --profile codex
```

## Syncing from Upstream

```bash
git fetch upstream
git merge upstream/main
pnpm build
```

## License

MIT
