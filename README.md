# sulishui-lark-agent

> Use Feishu / Lark as the chat interface for Claude Code and Codex CLI.

Supports **Claude Code + Codex CLI**, **macOS + Windows + Linux**, streaming Feishu cards, message reactions, file/image handling, multiple workspaces, and background services.

Project URL: <https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge>

[中文文档](./README.zh.md)

---

## Easiest Installation

Do not install it manually command by command. Copy this prompt into **Claude Code** or **Codex**:

```text
Please install and configure this Feishu/Lark coding-agent bridge for me:

https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge

Goals:
1. Install sulishui-lark-agent on this computer.
2. Automatically check and fix missing prerequisites, including Node.js >= 20.12, npm/corepack/pnpm, git, and an available local claude or codex CLI.
3. Prefer installing from GitHub with:
   npm install -g github:WilsonSU-A/sulishui-lark-coding-agent-bridge
4. If global GitHub installation fails, clone the repo, install dependencies, build it, and use npm link or an equivalent local install.
5. Do not overwrite my existing ~/.lark-channel config. Preserve and reuse existing claude/codex profiles if present.
6. After installation, run sulishui-lark-agent --version to verify the command works.
7. Help me bind a Feishu/Lark PersonalAgent app. If I already have App ID and App Secret, use them; otherwise use the QR/first-run flow.
8. Start the background service after setup:
   sulishui-lark-agent start --profile codex --agent codex
   or start the claude profile if I choose Claude Code.
9. Finally, run sulishui-lark-agent status --profile codex to verify the bot is online.

Handle everything automatically. Only stop when you need me to enter Feishu App ID, App Secret, scan a QR code, log in to GitHub/Feishu, or approve an administrator permission prompt.
```

The agent should handle installation, build, startup, and verification for you.

---

## What It Does

`sulishui-lark-agent` is a local bridge service. It forwards Feishu/Lark messages to Claude Code or Codex CLI running on your machine, then sends the agent's response back as Feishu cards.

You can:

- DM the bot and ask Codex or Claude Code to work on your local project.
- Mention the bot in a group chat.
- Send files and images for local processing.
- Use `/status`, `/new`, `/cd`, `/ws`, and `/stop` to control sessions and workspaces.

---

## Difference From Upstream

This project is based on [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) and remains compatible with upstream behavior.

| Capability | sulishui-lark-agent |
|---|---|
| Claude Code | Supported |
| Codex CLI | Supported |
| macOS | launchd background service |
| Windows | Task Scheduler background service |
| Linux | systemd background service |
| New CLI | `sulishui-lark-agent` |
| Legacy alias | `lark-channel-bridge` still works |
| Config compatibility | Reuses `~/.lark-channel`; existing profiles do not need migration |

---

## Manual Commands

If you prefer manual operation:

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
