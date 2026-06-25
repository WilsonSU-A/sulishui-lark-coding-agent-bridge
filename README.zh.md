# sulishui-lark-agent

> `sulishui-lark-coding-agent-bridge` · 把飞书/Lark 变成本地编程助手的聊天入口

通过飞书私聊或群 @ 对接到本机 Claude Code 或 Codex CLI，支持流式卡片、会话延续、图片文件处理、多工作空间、交互卡片按钮。扫码一键绑定，macOS / Linux / Windows 三平台后台服务。

**兼容说明：** 旧命令 `lark-channel-bridge` 仍然可用，已有 profile 和配置无需迁移。

> 本项目基于 [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) 构建，感谢上游项目。

[English README](./README.md)

关于能实现的效果，详情可以阅读[飞书文档](https://larkcommunity.feishu.cn/docx/OaRIdFIRFoLM3xxTmKwcetHqn5e)

## 主要功能

- 在飞书私聊直接发消息，或在群里 `@bot`，把任务转给本机 Claude Code / Codex CLI。
- **流式卡片**：文本回复和工具调用实时更新在同一张卡片上。
- **会话延续**：每个聊天、话题或文档评论有自己的会话，不会互相串。
- **排队与消息合并**：短时间连续发送的消息会合并处理；任务运行中收到的普通消息会排队到下一轮，`/new`、`/cd`、`/ws use`、`/stop` 这类命令可以中断当前任务。
- **多工作空间**：用 `/cd` 切换当前项目，用 `/ws` 保存和复用常用项目目录。
- **图片 / 文件**：直接发给 bot，bridge 下载到本地后交给本机 agent 处理。
- **卡片按钮**：`/help`、`/ws list`、`/status` 返回可点击的交互卡片。

## 前置条件

- Node.js **>= 20.12.0**
- 本机至少安装并登录一个 agent：
  - Claude Code：`claude`，安装说明：https://docs.anthropic.com/en/docs/claude-code/quickstart
  - Codex CLI：`codex`，安装说明：https://developers.openai.com/codex/cli
- 一个飞书 / Lark PersonalAgent 应用。首次启动的扫码向导可以帮你创建并绑定。

## 安装

```bash
npm i -g sulishui-lark-coding-agent-bridge
# 或
pnpm add -g sulishui-lark-coding-agent-bridge
```

## 首次启动

```bash
sulishui-lark-agent run
```

第一次运行会进入扫码向导：

1. 终端渲染二维码。
2. 用飞书 App 扫码。
3. 选择或创建 PersonalAgent 应用。
4. 如果终端提示，选择本次要初始化的 agent。
5. 成功后配置写入 `~/.lark-channel/config.json`。

没有指定项目目录也可以启动。bridge 会创建一个 profile 托管的默认工作目录；启动后在飞书里发送 `/cd <path>` 切到实际项目。

如果已经有 PersonalAgent app，可以在初始化时传 `--app-id` 跳过创建应用流程；命令会提示输入 App Secret。

```bash
sulishui-lark-agent run --app-id cli_xxx
# 或直接初始化并启动后台服务
sulishui-lark-agent start --app-id cli_xxx
```

Lark 国际版应用可加 `--tenant lark`。

## 后台运行（macOS / Linux / Windows）

```bash
sulishui-lark-agent start
sulishui-lark-agent status
sulishui-lark-agent stop
```

- **macOS**：launchd 用户代理 `ai.sulishui-lark-agent.bot.<profile>`
- **Linux**：systemd 用户单元 `sulishui-lark-agent.bot.<profile>.service`
- **Windows**：Task Scheduler 任务 `SulishuiLarkAgent.Bot.<profile>`

daemon 日志在 `~/.lark-channel/profiles/<profile>/logs/daemon/`。

## 多 Agent 多 Profile

同一台机器可以同时跑多个 bot，各自绑定不同的 agent 或不同的飞书应用：

```bash
sulishui-lark-agent start --profile claude --agent claude
sulishui-lark-agent start --profile codex --agent codex
```

### 管理 Profile

```bash
sulishui-lark-agent restart --profile codex
sulishui-lark-agent status --profile codex
```

## CLI 命令速查

```
sulishui-lark-agent run [--profile <name>] [--agent claude|codex] [--workspace <path>] [-c <config>]
sulishui-lark-agent migrate [--profile <name>] [--agent claude|codex]
sulishui-lark-agent ps
sulishui-lark-agent kill <id|#>
sulishui-lark-agent --help
```

### Profile 管理子命令

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

每个 profile 都使用当前 profile 的 lark-cli 目录：`~/.lark-channel/profiles/<profile>/lark-cli`。agent 子进程会收到指向这个目录的 `LARKSUITE_CLI_CONFIG_DIR`，所以一个 profile 里的个人授权不会共享给另一个 profile。

## 工作空间

bridge 接管并管理 agent 的工作目录。每个 profile 默认创建一个 `~/.lark-channel-workspaces/<profile>/default` 作为初始工作空间。可通过 `/cd` / `/ws` 飞书命令切换和保存常用目录，状态保存在 `~/.lark-channel/profiles/<profile>/workspaces.json`。

## 回退方案

```bash
# 切回上游版本（如果需要对比或回退）
pnpm add -g lark-channel-bridge@0.3.1
# 或直接使用别名
lark-channel-bridge run --profile codex
```

## 发送给 Agent 的消息

每条消息顶部会带一个 `<bridge_context>` 块，包含 chatId、senderId、senderName、senderType 和 mentions。多消息合并送达时每条前面会有 `[名字]:` 标注（bridge 注入的展示格式，不是消息原文）。Agent 还会看到：

- **引用回复**：`<quoted_message>` 块包含被引用原文，让你能理解上下文。
- **交互卡片**：`<interactive_card>` 块包含卡片的完整 JSON，可以解析按钮和表单字段。

## 文件结构速查

| 路径 | 内容 |
|------|------|
| `~/.lark-channel/config.json` | root config，包含 profiles 和 active profile |
| `~/.lark-channel/active-profile` | 最近选择的 profile |
| `~/.lark-channel/profiles/<profile>/sessions.json` | 会话状态 |
| `~/.lark-channel/profiles/<profile>/sessions.json.catalog.json` | agent-aware 会话索引 |
| `~/.lark-channel/profiles/<profile>/workspaces.json` | 当前和命名工作空间绑定 |
| `~/.lark-channel/profiles/<profile>/secrets.enc` | profile 本地加密 secret |
| `~/.lark-channel/profiles/<profile>/lark-cli/` | 当前 profile 的 lark-cli 目录 |
| `~/.lark-channel/profiles/<profile>/media/` | 附件缓存 |
| `~/.lark-channel/profiles/<profile>/logs/` | 结构化运行日志 |
| `~/.lark-channel/registry/processes.json` | 本机进程注册表 |
| `~/.lark-channel/registry/locks/` | profile lock 和 app lock |

## 授予他人权限

在飞书里发 `/invite`，会弹出一个模板卡；选对应的群或把用户 open_id 填进去，保存到当前 profile 的 `access` 字段。open_id 可以用 `/whoami` 查看；或直接在飞书客户端里点"资料页"复制。

不想在飞书里点的话，`/invite`、`/config` 背后写的是 `~/.lark-channel/config.json` 中对应 profile 的 `access` 字段。空白名单表示这个名单没人，不表示所有人都能用。下面只是 profile 里的字段片段，不要整段覆盖 `config.json`：

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

更多细节见 [policy/access.ts](src/policy/access.ts)。

## 接入已有应用

如果你已经有 PersonalAgent 应用不想走扫码创建，可以直接传 `--app-id`。命令会提示输入 App Secret（不会回显到终端也不会写入明文配置文件——bridge 会把它加密存到 `~/.lark-channel/profiles/<profile>/secrets.enc`）。

```bash
sulishui-lark-agent run --app-id cli_abc123
# 然后输入 App Secret
```

观察新配置是否生效：

```bash
grep '"event":"enter"' ~/.lark-channel/profiles/<profile>/logs/bridge-$(date +%Y%m%d).jsonl | tail -5
```

## 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `LARK_CHANNEL_HOME` | 覆盖配置根目录 | `~/.lark-channel` |
| `LARK_CHANNEL_PROFILE` | 覆盖当前 profile（用于 daemon） | active profile |
| `LARK_CHANNEL_CODEX_BIN` | 覆盖 Codex CLI 路径 | `codex`（从 `$PATH` 找） |
| `LARK_CHANNEL_TELEMETRY_MODULE` | 可插拔遥测适配器 | 关闭 |

详见 `src/cli/preflight.ts`、`src/core/telemetry.ts`。

## 从上游更新

本项目基于 [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge)。如需同步上游更新：

```bash
git fetch upstream
git merge upstream/main
# 解决冲突后重新构建
pnpm build
```
