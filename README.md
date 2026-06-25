# sulishui-lark-agent

> `sulishui-lark-coding-agent-bridge` · Turn Feishu/Lark into a chat interface for your local coding agents

Bridge Feishu/Lark messages to Claude Code or Codex CLI running on your machine. Streaming cards, session continuity, image/file processing, multi-workspace, interactive card buttons. One-command setup, macOS/Linux/Windows background service.

**Compatibility:** Legacy command `lark-channel-bridge` is still available. Existing profiles and config require no migration.

> Built on [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge). 

[中文文档](./README.zh.md)

For what this can do, see the [Feishu documentation](https://larkcommunity.feishu.cn/docx/OaRIdFIRFoLM3xxTmKwcetHqn5e) (in Chinese).

## Features

- Message Claude Code / Codex CLI via Feishu private chat or group `@bot`.
- **Streaming cards**: text and tool calls update live on the same card.
- **Session continuity**: each chat, topic, or doc comment has its own session.
- **Message queuing & merging**: rapid-fire messages merge; runtime messages queue until the current run finishes. `/new`, `/cd`, `/ws use`, `/stop` interrupt mid-run.
- **Multi-workspace**: `/cd` to switch projects, `/ws` to save and reuse directories.
- **Images / files**: send attachments directly to the bot; bridge downloads them locally for the agent.
- **Card buttons**: `/help`, `/ws list`, `/status` return clickable interactive cards.

## Prerequisites

- Node.js **>= 20.12.0**
- At least one agent installed and logged in:
  - Claude Code: `claude` ([install guide](https://docs.anthropic.com/en/docs/claude-code/quickstart))
  - Codex CLI: `codex` ([install guide](https://developers.openai.com/codex/cli))
- A Feishu/Lark PersonalAgent app. The first-run QR wizard creates and binds one for you.

## Install

```bash
npm i -g sulishui-lark-coding-agent-bridge
# or
pnpm add -g sulishui-lark-coding-agent-bridge
```

## First Run

```bash
sulishui-lark-agent run
```

The first run walks you through a QR-code setup:

1. Terminal renders a QR code.
2. Scan it with the Feishu app.
3. Select or create a PersonalAgent app.
4. If prompted, pick which agent to initialize.
5. Config is written to `~/.lark-channel/config.json`.

No project directory needed. Bridge creates a profile-managed default workspace; use `/cd <path>` in Feishu to switch later.

If you already have a PersonalAgent app, pass `--app-id` to skip creation:

```bash
sulishui-lark-agent run --app-id cli_xxx
# or bootstrap directly into a background service
sulishui-lark-agent start --app-id cli_xxx
```

Use `--tenant lark` for Lark international tenants.

## Background Service (macOS / Linux / Windows)

```bash
sulishui-lark-agent start
sulishui-lark-agent status
sulishui-lark-agent stop
```

- **macOS**: launchd user agent `ai.sulishui-lark-agent.bot.<profile>`
- **Linux**: systemd user unit `sulishui-lark-agent.bot.<profile>.service`
- **Windows**: Task Scheduler task `SulishuiLarkAgent.Bot.<profile>`

Daemon logs are under `~/.lark-channel/profiles/<profile>/logs/daemon/`.

## Multiple Agents & Profiles

Run multiple bots simultaneously, each bound to a different agent or Feishu app:

```bash
sulishui-lark-agent start --profile claude --agent claude
sulishui-lark-agent start --profile codex --agent codex
```

### Profile Management

```bash
sulishui-lark-agent restart --profile codex
sulishui-lark-agent status --profile codex
```

## CLI Quick Reference

```
sulishui-lark-agent run [--profile <name>] [--agent claude|codex] [--workspace <path>] [-c <config>]
sulishui-lark-agent migrate [--profile <name>] [--agent claude|codex]
sulishui-lark-agent ps
sulishui-lark-agent kill <id|#>
sulishui-lark-agent --help
```

### Profile Subcommands

```bash
sulishui-lark-agent profile create claude --agent claude
sulishui-lark-agent profile create codex --agent codex
sulishui-lark-agent profile list
sulishui-lark-agent profile use <name>
sulishui-lark-agent profile remove <name>
sulishui-lark-agent profile remove <name> --purge --yes
sulishui-lark-agent profile export <name> [--output ./profile.json] [--force]
sulishui-lark-agent profile export <name> --include-secrets --yes
```

Each profile uses a profile-local lark-cli directory at `~/.lark-channel/profiles/<profile>/lark-cli`. The agent process receives `LARKSUITE_CLI_CONFIG_DIR` for that directory, so personal authorization in one profile is not shared with another profile.

## Workspaces

Bridge manages agent working directories. Each profile starts with `~/.lark-channel-workspaces/<profile>/default`. Use `/cd` and `/ws` commands in Feishu to switch and save directories; state is persisted in `~/.lark-channel/profiles/<profile>/workspaces.json`.

## Fallback / Rollback

```bash
# Switch back to upstream version if needed
pnpm add -g lark-channel-bridge@0.3.1
# or use the alias directly
lark-channel-bridge run --profile codex
```

## File Layout Reference

| Path | Content |
|------|---------|
| `~/.lark-channel/config.json` | Root config with profiles and active profile |
| `~/.lark-channel/active-profile` | Last selected profile |
| `~/.lark-channel/profiles/<profile>/sessions.json` | Session state |
| `~/.lark-channel/profiles/<profile>/sessions.json.catalog.json` | Agent-aware session catalog |
| `~/.lark-channel/profiles/<profile>/workspaces.json` | Current and named workspace bindings |
| `~/.lark-channel/profiles/<profile>/secrets.enc` | Profile-local encrypted secrets |
| `~/.lark-channel/profiles/<profile>/lark-cli/` | Profile-local lark-cli directory |
| `~/.lark-channel/profiles/<profile>/media/` | Attachment cache |
| `~/.lark-channel/profiles/<profile>/logs/` | Structured run logs |
| `~/.lark-channel/registry/processes.json` | Local process registry |
| `~/.lark-channel/registry/locks/` | Profile and app locks |

## Granting Access to Others

Send `/invite` in Feishu for a template card; fill in the group or user open_id you want to grant access to. Use `/whoami` to find your own open_id, or copy it from the profile page in the Feishu client.

The underlying write target is the `access` field in `~/.lark-channel/config.json` for the current profile. Empty lists mean nobody from that list, not open access.

```json
{
  "profiles": {
    "claude": {
      "access": {
        "users": ["ou_xxx"],
        "groups": ["oc_yyy"]
      }
    }
  }
}
```

More details in [policy/access.ts](src/policy/access.ts).

## Using Your Own App

Skip the QR flow with `--app-id`. The command prompts for the App Secret (not echoed, not written in plaintext — encrypted into `~/.lark-channel/profiles/<profile>/secrets.enc`).

```bash
sulishui-lark-agent run --app-id cli_abc123
# then enter App Secret
```

Verify the new config took effect:

```bash
grep '"event":"enter"' ~/.lark-channel/profiles/<profile>/logs/bridge-$(date +%Y%m%d).jsonl | tail -5
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `LARK_CHANNEL_HOME` | Override config root | `~/.lark-channel` |
| `LARK_CHANNEL_PROFILE` | Override current profile (daemon use) | active profile |
| `LARK_CHANNEL_CODEX_BIN` | Override Codex CLI path | `codex` (from `$PATH`) |
| `LARK_CHANNEL_TELEMETRY_MODULE` | Pluggable telemetry adapter | disabled |

## Syncing from Upstream

Built on [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge). To sync upstream updates:

```bash
git fetch upstream
git merge upstream/main
# resolve conflicts, then rebuild
pnpm build
```

## License

MIT
