# Motivoo Agent Plugin

让你的 AI Agent（Codex、Claude Code、Cursor 等）直接用 Motivoo 生成图片、视频、配音、音乐与音效，结果同步到你的 Motivoo 画布。

- Remote MCP：`https://motivoo.tv/mcp`（Streamable HTTP，OAuth 授权，无需 API Key）
- 接入指南：https://motivoo.tv/agent

## Codex

```bash
codex plugin marketplace add https://github.com/motivoo-tv/motivoo-plugin.git --ref main
codex plugin add motivoo@motivoo
```

新建任务后在 MCP 连接管理里完成 Motivoo 账户授权。更新：`codex plugin marketplace upgrade motivoo`。

## Claude Code

```bash
/plugin marketplace add https://github.com/motivoo-tv/motivoo-plugin.git
/plugin install motivoo@motivoo
```

或只接入 MCP：`claude mcp add --transport http motivoo https://motivoo.tv/mcp`，然后在 `/mcp` 中完成授权。

## 其他支持 Remote MCP 的客户端

新建一个名为 Motivoo 的连接器，地址填 `https://motivoo.tv/mcp`，保存后完成账户授权。Skill 可从 https://motivoo.tv/api/agent/skills.zip 下载后放入客户端的 skills 目录。

## 费用与安全

- 按次消耗账户 Motis 积分；单次超过你设置的阈值会先征求确认，每日合计不超过你设置的上限（默认 3000 Motis / 天、单次 50 Motis 需确认）。
- 限额与已连接的 Agent 在 https://motivoo.tv/space/agents 管理，可随时断开。
- 本仓库不包含任何凭据；授权通过 OAuth 在浏览器中完成。
