---
name: vibe-usage
description: 查询和同步 AI 编程工具的 token 用量（VibeCafé 旗下 Vibe Usage 数据）。
---

# Vibe Usage

Track and answer questions about the user's AI coding token spend via [Vibe Usage](https://vibecafe.ai/usage) (VibeCafé).

## 查询用量（默认行为）

当用户问以下问题时，运行命令并**原样展示输出**（不要总结、不要换算单位、不要翻译）：

| 用户说... | 运行 |
|---|---|
| 我这周花了多少 / 查 usage / 看看消费 | `npx @vibe-cafe/vibe-usage summary` |
| 今天花了多少 / 今天 token | `npx @vibe-cafe/vibe-usage summary --days 1` |
| 本月花费 / 这个月用量 | `npx @vibe-cafe/vibe-usage summary --days 30` |
| 哪个模型最贵 / 模型对比 | `npx @vibe-cafe/vibe-usage summary`（输出已按模型拆） |
| 哪个项目花得最多 | `npx @vibe-cafe/vibe-usage summary`（输出已按项目拆） |

输出是 markdown 表格，直接展示给用户即可。

## 维护命令

| 用户说... | 运行 |
|---|---|
| 同步数据 / 上传 usage / 数据没更新 | `npx @vibe-cafe/vibe-usage sync` |
| 看 daemon 状态 / 后台同步还在跑吗 | `npx @vibe-cafe/vibe-usage daemon status` |
| 启动后台同步 | `npx @vibe-cafe/vibe-usage daemon install` |
| 重置数据 / 重新上传 | `npx @vibe-cafe/vibe-usage reset` |

## 注意

- `summary` 读 `~/.vibe-usage/config.json` 里已有的 API key，不需要用户额外输入
- summary 输出已是 markdown，**原样展示，不要复述**
- 用户没装过 vibe-usage？提示运行 `npx @vibe-cafe/vibe-usage` 先用浏览器登录链接账号
- 支持的工具：Claude Code, Codex CLI, Copilot CLI, Gemini CLI, OpenCode, OpenClaw, Qwen Code, Kimi Code, Amp, Droid 等
