# conversation-showcase

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that turns your AI conversations into shareable social media images.

一个 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 技能，将你的 AI 对话过程转化为适合社交媒体分享的系列图。

> Built by [觉然 JORAN.ONLINE](https://joran.online)

---

## What it does / 它做什么

After completing a task with Claude Code, run `/conversation-showcase` to generate a polished image series that tells the story of your collaboration — the problem, the process, and the result.

在 Claude Code 中完成一项任务后，输入 `/conversation-showcase`，自动生成一组精美的系列图，展示你与 AI 协作的完整过程——问题、推进、结果。

**Cover page** (封面) — A poster-style summary of what was accomplished:

```
┌─────────────────────────────────┐
│  ● ● ●          ✦ Claude Code   │
│                                 │
│  AUTOMATION                     │
│  用 Claude Code 搭建             │
│  PEPE 自动交易机器人              │
│  ──                              │
│  通过多轮对话，将个人交易经验     │
│  与 AI 编程能力结合...            │
│                                 │
│  ┌───────────────────────────┐  │
│  │ 🧠 人：策略方向 + 经验反馈 │  │
│  │ 🤖 AI：编码 + 200 轮回测   │  │
│  │ 💰 三笔实盘全部盈利        │  │
│  └───────────────────────────┘  │
│              ● ○                │
│         ✦ Claude Code           │
└─────────────────────────────────┘
```

**Body page** (正文) — The actual dialogue showing human expertise + AI execution:

```
┌─────────────────────────────────┐
│  ● ● ●          ✦ Claude Code   │
│     对话过程 · 从需求到实盘       │
│                                 │
│     ┌─────────────────────┐  U  │
│     │ 帮我做一个自动交易... │     │
│     └─────────────────────┘     │
│  ⚡ Actions                      │
│  📝 Create bot.py               │
│  🧪 200 轮回测                   │
│                                 │
│  ✦ ┌─────────────────────────┐  │
│    │ 布林带触下轨胜率 94%...  │  │
│    └─────────────────────────┘  │
│              ○ ●                │
│         ✦ Claude Code           │
└─────────────────────────────────┘
```

## Features / 特性

- **Anthropic design** — Warm slate palette from anthropic.com (`#e8e6dc` background, `#d97757` clay accent)
- **Cover + Body format** — Poster-style cover (no dialogue) + dense body pages (4-6 elements each)
- **Claude Code little robot avatar** — Cute robot mascot (antenna + head + ear knobs + visor + eyes) rendered as SVG, clay color on cream circle
- **Honest tone** — Shows human-AI collaboration, not "one-click magic". User expertise and iterative feedback are visible.
- **小红书 optimized** — Default 1080×1440 (3:4), large fonts readable on mobile
- **Chrome headless rendering** — 2x Retina PNG output, no dependencies beyond Chrome
- **Multi-page series** — 2-3 pages with page indicator dots, natural content flow

## Install / 安装

```bash
# Clone into your Claude Code skills directory
git clone https://github.com/cocohahaha/conversation-showcase.git \
  ~/.claude/skills/conversation-showcase
```

After cloning, the skill is immediately available in all Claude Code sessions.

克隆到 Claude Code 技能目录后，所有会话中均可立即使用。

## Usage / 使用

In any Claude Code conversation, after completing work:

```bash
/conversation-showcase                    # Default: Anthropic theme, 小红书 3:4
/conversation-showcase --theme dark       # Dark theme
/conversation-showcase --aspect square    # 1:1 for Instagram
/conversation-showcase --aspect landscape # 16:9 for Twitter/X
```

### Options

| Option | Values | Default |
|--------|--------|---------|
| `--theme` | `anthropic`, `dark`, `terminal` | `anthropic` |
| `--aspect` | `xhs` (3:4), `portrait`, `square`, `story`, `landscape` | `xhs` |
| `--title` | Custom title | Auto-generated |
| `--user` | Avatar text | `U` |

### Output

Images are saved to `~/Desktop/conversation-showcase/`:
```
showcase-{topic}-{time}-01.png   # Cover
showcase-{topic}-{time}-02.png   # Body
```

## Design Philosophy / 设计理念

This skill was built with a specific stance on how AI work should be presented:

**Show the collaboration, not the magic.** Real AI-assisted work involves human expertise, iterative feedback, and domain knowledge — not "one prompt" miracles. The cover page credits both human and AI contributions. The body pages show the back-and-forth of refinement.

这个技能对 AI 成果的展示方式有明确的态度：

**展示协作过程，而不是魔法。** 真正的 AI 辅助工作包含人的专业经验、反复的反馈调整和领域知识，不存在"一句话生成"的奇迹。封面页同时展示人和 AI 的贡献，正文页呈现迭代打磨的过程。

## Requirements / 依赖

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (CLI)
- Google Chrome (for headless screenshot rendering)
- macOS (tested), Linux (should work), Windows (untested)

## License

MIT

---

Made by [觉然 JORAN.ONLINE](https://joran.online)
