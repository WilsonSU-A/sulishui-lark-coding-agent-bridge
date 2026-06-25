# sulishui-lark-agent

> `sulishui-lark-coding-agent-bridge` · 把飞书/Lark 变成本地编程助手的聊天入口

通过飞书私聊或群 @ 对接到本机 Claude Code 或 Codex CLI，支持流式卡片、会话延续、图片文件处理、多工作空间、交互卡片按钮。扫码一键绑定，macOS / Linux / Windows 三平台后台服务。

**兼容说明：** 旧命令 `lark-channel-bridge` 仍然可用，已有 profile 和配置无需迁移。

> 基于 [zarazhangrui/lark-coding-agent-bridge](https://github.com/zarazhangrui/lark-coding-agent-bridge) 构建。

[English README](./README.md) | [效果详见飞书文档](https://larkcommunity.feishu.cn/docx/OaRIdFIRFoLM3xxTmKwcetHqn5e)

## 安装

```bash
npm i -g sulishui-lark-coding-agent-bridge
```

## 快速开始

```bash
sulishui-lark-agent run
```

扫码绑定后即可在飞书里和 Claude Code / Codex CLI 对话。

## License

MIT
