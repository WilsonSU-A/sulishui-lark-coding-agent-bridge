# sulishui-lark-agent

> Use Feishu / Lark as the chat interface for Claude Code and Codex CLI.

Supports **Claude Code + Codex CLI**, **macOS + Windows + Linux**, streaming Feishu cards, message reactions, file/image handling, multiple workspaces, and background services.

Project URL: <https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge>

[中文文档](./README.zh.md)

---

## Easiest Installation

Do not install it manually command by command, and do not manually copy App ID or App Secret.

Copy this prompt into **Claude Code** or **Codex**:

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
7. Bind the Feishu/Lark PersonalAgent through sulishui-lark-agent's first-run authorization flow. Do not ask me to manually type or paste App ID or App Secret, and do not put secrets in chat history.
8. If authorization is needed, open the Feishu/Lark authorization link or show the official QR code, then let me choose the agent and approve permissions in Feishu/Lark.
9. After authorization, start the background service:
   sulishui-lark-agent start --profile codex --agent codex
   or start the claude profile if I choose Claude Code.
10. Finally, run sulishui-lark-agent status --profile codex to verify the bot is online.

Handle everything automatically. Only stop when I need to click the Feishu/Lark authorization link, scan a QR code, choose an agent, approve permissions, log in to GitHub/Feishu, or approve an administrator permission prompt.
```

The agent should handle environment checks, installation, authorization guidance, startup, and verification for you.

---

## Command-Line Install (Optional)

The agent-driven flow above is recommended. If you prefer the command line:

```bash
npm install -g github:WilsonSU-A/sulishui-lark-coding-agent-bridge
sulishui-lark-agent --version
sulishui-lark-agent run
```

`run` opens the Feishu/Lark authorization link or shows the official QR code. Choose the agent and approve permissions in Feishu/Lark; do not manually type or paste App ID or App Secret.

If the global install fails, use the source install fallback:

```bash
git clone https://github.com/WilsonSU-A/sulishui-lark-coding-agent-bridge.git
cd sulishui-lark-coding-agent-bridge
corepack enable
pnpm install
pnpm build
npm link
sulishui-lark-agent --version
```

---

## Secure Binding Flow

The recommended flow is **authorization link / official QR code**:

1. The agent starts `sulishui-lark-agent run` or the initialization flow.
2. The terminal shows a Feishu/Lark authorization link or official QR code.
3. The user chooses the PersonalAgent to bind inside Feishu/Lark.
4. The user approves the required permissions inside Feishu/Lark.
5. The bridge stores the authorization result in local encrypted config.
6. The agent starts the background service and verifies with `status`.

Security principles:

- Do not ask users to manually copy App ID.
- Do not ask users to manually copy App Secret.
- Do not expose secrets in chat history.
- Do not overwrite existing `~/.lark-channel` config.
- Reuse and preserve existing profiles whenever possible.

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
| Recommended binding | Feishu/Lark authorization link or official QR code, no manual secrets |

---

## Manual Commands (Fallback)

Most users should not need this section. Use it only for debugging:

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
