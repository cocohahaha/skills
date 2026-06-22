---
name: joran-wrap-up
description: "Generates a beautiful daily recap image summarizing ALL AI coding agent sessions of the day — not just the current one. Platform-agnostic (Claude Code, Gemini CLI, OpenCode, etc.). Light, friendly, social-media-ready design. Use when user asks to 'wrap up', '收工', '今日总结', 'daily recap', 'session summary', or wants to share what they accomplished today. by JORAN 觉然"
---

# Wrap-Up — 今日收工报告

Generate a single shareable social media image that recaps everything accomplished across **ALL AI coding agent sessions today** — not just the current one. Platform-agnostic: works with Claude Code, Gemini CLI, OpenCode, Cursor, or any AI coding tool. Designed for honest, grounded "what I did today" posts — visually strong, 小白友好, readable at a glance on mobile. Tone is sincere, not showy.

## Usage

```bash
/wrap-up                    # Light theme (default)
/wrap-up --theme dark       # Force dark theme
/wrap-up --aspect xhs       # 小红书 3:4 (default)
/wrap-up --aspect story     # Stories 9:16
```

## Options

| Option | Values | Default |
|--------|--------|---------|
| `--theme` | `light`, `dark` | `light` |
| `--aspect` | `xhs` (1080×1440), `story` (1080×1920), `square` (1080×1080) | `xhs` |
| `--user` | Custom user name | Auto-detect from CLAUDE.md/memory, fallback "U" |

## Output

```
~/Desktop/conversation-showcase/
├── wrap-up-{YYYY-MM-DD}-{HHMMSS}.png          # Final image
└── html/
    └── wrap-up-{YYYY-MM-DD}-{HHMMSS}.html     # Source HTML (separate folder)
```

## Workflow

### Step 0: Scan Today's All Sessions (CROSS-SESSION — the key differentiator)

We summarize the ENTIRE day, not just the current conversation.

1. **Find today's conversation logs:**
   ```bash
   # Find all JSONL conversation files modified today across all projects
   find ~/.claude/projects/ -name "*.jsonl" -newermt "$(date +%Y-%m-%d)" -type f 2>/dev/null
   ```
   Also check: `~/.claude/conversations/`, `~/.claude/projects/*/conversations/`

2. **For each conversation file found today (excluding the current session):**
   - Read the file (JSONL — one JSON object per line)
   - Extract `role: "user"` messages to understand what the user asked
   - Extract `role: "assistant"` messages with tool calls to understand what was done
   - Look for key signals: file writes/edits (code written), bash commands (deployments), web searches, agent dispatches
   - Build a brief summary of that session's accomplishments
   - **Performance**: only read the first and last ~200 lines of very large files — enough to get the gist without blocking

3. **Combine with current session:**
   - The current conversation is analyzed directly from context (as before)
   - Other sessions' summaries are merged, deduplicating overlapping work
   - Sort all activities chronologically
   - Count total sessions for the badge

4. **If no other sessions found:** Fall back to current-session-only mode. Still show "今日 1 个 session".

### Step 1: Extract & Synthesize

From ALL sessions combined, extract:

1. **Honest headline** — a grounded, casual one-liner summarizing the WHOLE DAY's work. This is the TITLE of the image. It should be honest about what was done, neither humble-bragging nor underselling.
   - Good: "语音输入工具做了个四平台版本"、"课程脚本 + 产品开发同步推进"、"从优化到重构，需求滚雪球的一天"
   - Bad: "今天一个人干了整个技术部的活"（炫耀、装逼）
   - Bad: "今日 AI 智能体收工报告"（too generic, no personality）
   - Bad: "完成了网站部署和支付系统集成"（too technical, too jargon-heavy）
   - **关键原则**: 说事实，不夸大。读起来像在记日记，不像在发战报
2. **Task list** — 4-6 things across ALL sessions, written as **casual short sentences anyone can understand**
   - Good: "搭了一个自动收款页面"
   - Bad: "创建 3 个 Stripe 产品 + Payment Links（¥20 / ¥399 / ¥799）"
   - Each task: one punchy main line + optional muted subtitle (≤8 chars)
   - **小白原则**: 如果你妈看不懂，就再简化一遍
3. **Session count** — show "今日 N 个 session" as a subtle pill badge near the header
4. **Agent dispatch log** — count and what each did across all sessions (shown as compact tags)
5. **Key metrics** — 3-5 numbers that look impressive (deployments, files, APIs, etc.)
6. **Snarky commentary** — see Step 2

### Step 2: Write the Honest Reflection (真诚复盘)

At the bottom of the recap, add ONE line of honest, grounded reflection about the WHOLE DAY.

**Tone rules (CRITICAL):**
- **Sincere and grounded** — like writing in your own journal at the end of the day
- NOT flattering or sycophantic ("你真厉害！" ← NO)
- NOT snarky or showing off ("一个人干了整个技术部的活" ← NO, 太装)
- NOT mean or self-deprecating
- Can mention what went well, what was tricky, or an honest observation
- Short — one sentence, max two
- Chinese preferred, mix English if natural

**Good examples (中肯、真诚):**
- "四个平台同时推进，跨平台编译确实是最耗时的环节。"
- "课程脚本写完才发现，把技术概念讲给小白听比写代码难多了。"
- "需求从'优化一下'滚到了全面重构，不过每一步都有明确交付物，还算踏实。"
- "网络不稳导致上传反复失败，最后换了方案才搞定——遇到问题换思路比死磕有效。"
- "今天大部分时间花在构建环境搭建上，真正写业务代码的时间其实不多。"

**Bad examples (避免):**
- "今天真是充实的一天！你太棒了！" ← 谄媚
- "一个下午干了别人一个季度的活" ← 装逼、炫耀
- "说好只改个按钮颜色，最后连公司都快注册了" ← 段子味太重，不真诚
- "辛苦了，期待明天继续合作！" ← 客服话术

### Step 2.5: Privacy & Sensitivity Filter (CRITICAL)

The generated image will be posted on social media. Before rendering, apply these rules:

**Crypto / Quantitative Trading:**
- NEVER mention 加密货币、币、crypto、token、PEPE、meme coin, or specific coin names
- Replace with vague terms: "量化策略"、"自动化交易系统"、"数据分析脚本"、"市场监控工具"
- This is a legal compliance requirement for mainland China — no exceptions

**Personal & Business Info:**
- Blur or omit: real email addresses, phone numbers, API keys, secret keys, account IDs
- Replace with generic references: "配置了支付接口" not "用 sk_live_xxx 连了 Stripe"
- Domain names of the user's own sites are OK to show (e.g., joran.online)
- Third-party API names are OK (e.g., Stripe, Cloudflare) — just don't show credentials

**Financial Details:**
- Don't show specific revenue, account balances, or transaction amounts in the image
- Pricing tiers are OK if they're public-facing (shown on the website)

**General Rule:**
- Ask yourself: "If this image went viral, would anything on it cause legal, financial, or privacy problems?" If yes, rephrase it.

### Step 3: Generate HTML

Create a single-page HTML file. The design must be **light, friendly, and readable at phone screen size** — big fonts, soft colors, generous whitespace, scannable in 3 seconds. Think: 小红书笔记 aesthetic, not 技术文档.

**Layout structure:**

```
┌──────────────────────────────────┐
│ ✦ {Platform} · 今日收工    📅    │  ← Header (platform + date, small)
│               今日 3 个 session  │  ← Session count pill badge
│                                  │
│  今天一个人干了整个技术部的活      │  ← Headline (HUGE, the hook)
│                                  │
│  ┌─ 4 ── 5 ── 3 ── 1 ─┐        │  ← Metrics (pastel pill bg)
│  │ 部署  链接  Agent 申诉│        │
│  └──────────────────────┘        │
│                                  │
│  💳 搭了一个自动收款页面          │  ← Task list (soft row cards)
│     Stripe 接入                  │
│  🌐 把官网从零做到上线            │
│  🔍 派了 3 个 Agent 做调研        │
│  📧 写了三套邮件模板              │
│  🎨 重新设计了落地页              │
│                                  │
│  [🤖 tag] [🤖 tag] [🤖 tag]     │  ← Agent tags (soft pills)
│                                  │
│  ─────── ✦ ───────               │  ← Soft divider
│  🤖💬👉 ┌──────────────────┐    │  ← Cute robot + bubble
│         │ ✦ Agent Says       │    │
│         │ "一句毒舌点评"     │    │
│         └──────────────────┘     │
│                                  │
│       ✦ {Platform}               │  ← Footer
└──────────────────────────────────┘
```

**Platform detection (header & footer branding):**
- Auto-detect which AI coding tool is running this skill
- Common platforms: `Claude Code`, `Gemini CLI`, `OpenCode`, `Cursor`, `Windsurf`, `Aider`
- If detection fails, fall back to generic `AI Coding Agent`
- Display as: `✦ {Platform} · 今日收工`

**CRITICAL: 设计原则 — 小白友好 & 社媒传播**
- The headline must be the BIGGEST text on the page — it's what stops the scroll
- Metrics use soft pastel pill backgrounds, not harsh dark boxes
- Task list should be scannable — one glance = understand what happened
- The Agent Says section should feel like a special moment, visually distinct
- Everything should be readable WITHOUT zooming in on a phone
- **Overall vibe: 温暖、轻松、有趣 — like a friend's social media post, not a corporate report**
- **NO dark/heavy/techy aesthetics — think 小红书/Instagram story, not GitHub Dashboard**
- Generous whitespace and padding between all sections (20px+ gaps)
- All corners well-rounded (12-16px radius)
- Subtle, soft shadows — not sharp drop shadows

**Visual design:**

#### Light theme (default — 干净有力)
Use JORAN brand palette: teal (#2a8a7a) as primary accent, warm gold (#c49a6c) as secondary, with sufficient contrast and saturation to be eye-catching.
```css
--bg: #f0eeeb;              /* 温暖米灰，比纯白有质感 */
--card-bg: #ffffff;
--text-primary: #1a1a1a;    /* 接近黑色，强对比 */
--text-muted: #7a7a7a;      /* 中灰，不是浅灰 */
--accent: #2a8a7a;           /* JORAN teal，品牌主色 */
--accent-secondary: #c49a6c; /* JORAN warm gold，品牌副色 */
--accent-light: #e6f3f0;     /* teal 淡底 */
--metrics-bg-1: #dceefb;     /* 蓝 pill — 饱和度提高 */
--metrics-bg-2: #d8f0d8;     /* 绿 pill */
--metrics-bg-3: #fce8d5;     /* 橙 pill */
--metrics-bg-4: #e8dff5;     /* 紫 pill */
--task-bg: #ffffff;          /* 白底卡片 + 明显 border */
--task-border: #e0ddd8;      /* 任务卡片边框 */
--agent-tag-bg: #d4ede6;     /* 更饱和的 teal pill */
--agent-tag-text: #1d6b5e;
--comment-bg: #f8f3ee;       /* 温暖底色 */
--comment-border: #c49a6c;   /* 用 warm gold 做边框 */
--divider: #d8d5d0;          /* 更深的分割线 */
--shadow: 0 4px 16px rgba(0,0,0,0.08);  /* 更明显的阴影 */
```

#### Dark theme (optional)
```css
--bg: #0d0d0d;
--card-bg: #1a1a1a;
--text-primary: #ebebeb;
--text-muted: #808080;
--accent: #3da896;           /* teal 亮版 */
--accent-secondary: #d4a87a;
--accent-light: #1a2e2a;
--metrics-bg-1: #162030;
--metrics-bg-2: #163020;
--metrics-bg-3: #302418;
--metrics-bg-4: #201830;
--task-bg: #1e1e1e;
--task-border: #333;
--agent-tag-bg: #1a3028;
--agent-tag-text: #5ec8a8;
--comment-bg: #1e1a16;
--comment-border: #d4a87a;
--divider: #2a2a2a;
--shadow: 0 4px 16px rgba(0,0,0,0.3);
```

**Typography (minimum sizes — 友好字体，不用等宽):**
- Headline: Inter 800, **48px**, line-height 1.2 — the biggest element
- Metric numbers: Inter 700, **32px** (不用等宽字体，更亲切)
- Metric labels: Inter 400, 14px, muted color
- Task main text: Inter 500, **20px**
- Task subtitle: Inter 400, 17px, muted color
- Agent tags: Inter 500, 14px
- Commentary: Inter 500 italic, **22px**
- Header/footer: Inter 500, 15px
- Date: Inter 400, 14px, muted
- Session count badge: Inter 500, 13px, accent color text, `--accent-light` pill background

**Task icons** (use emoji, one per task):
💳 支付/Payment | 🌐 网站/Website | 📚 课程/Course | 🔧 工具/Tools | 📊 分析/Analysis | 🎨 设计/Design | 📦 部署/Deploy | 🔍 调研/Research | 🤖 AI/Agent | 📸 Showcase | 🏢 商务/Business | 🚫 删除/Remove | ✉️ 邮件/Email | 📧 模板/Template | 🎵 音乐/Music | 📱 移动端/Mobile

**Task list styling:**
- Each task is a full-width row with soft light background (`--task-bg`), well-rounded corners (12px)
- Subtle left accent border (4px `--accent`) for visual rhythm
- Icon on the left, main text in `--text-primary`, subtitle in `--text-muted` after em dash
- NO category labels or grids — just icon + text, one line per task
- Keep it to 4-6 tasks max — merge related work into one line
- Generous vertical padding (16px) and gap between rows (12px)

**Metrics bar styling:**
- Each metric is a rounded pill (16px radius) with its own pastel background color
- Number on top (large, `--text-primary`), label below (small, muted)
- Arranged in a flex row with equal spacing
- Soft shadow (`--shadow`)

**Agent tags styling:**
- Compact pill/tag shape, inline row
- Soft green-tinted background (`--agent-tag-bg`) with matching text
- Format: "🤖 [what it did] ✓"
- All in one horizontal row, wrapping if needed

**Commentary box styling — Agent Says（CRITICAL: must be cute, not corporate）:**
- Visual separator above: thin line in `--divider` color
- Layout: cute AI robot icon (80x80) on the left + speech bubble on the right
- **Robot icon design**: use a cute, round robot face — render via CSS/SVG or use a friendly robot emoji composition. The robot should look approachable and playful, NOT corporate or sci-fi. Think: 圆圆的脑袋 + 天线 + 笑眼, accent color as primary fill, `--accent-light` background, rounded square container with soft accent border
- Pointing hand emoji (👉) with subtle CSS bounce animation between robot and bubble
- Speech bubble: well-rounded corners (16px), accent border, `--comment-bg` background
- Label: "✦ Agent Says" (platform-agnostic, friendly tone)
- Key phrases in the commentary wrapped in `<em>` with accent color
- Right-top corner of bubble: time-of-day icon only (🌙 night, ☀️ day)
- This section should feel like the payoff — the reward for reading the whole image

**Writing style for task descriptions (CRITICAL — 小白友好):**
- Write like you're telling a friend what you did today, NOT writing a commit log
- Use verbs: 搭了、砍了、改了、写了、派了
- Keep each line under 15 characters for the main text
- The muted subtitle adds context, but the main text must stand alone
- **小白测试**: 如果你妈/不懂代码的朋友看不懂，就再简化一遍
- NO jargon in main text: 不写 "CI/CD pipeline"，写 "自动部署流程"
- NO English-heavy lines: 不写 "Stripe Payment Links"，写 "收款链接"

### Step 4: Theme Selection

Default is **light theme**. Only use dark when user explicitly passes `--theme dark`.

### Step 5: Render to PNG

```bash
# Save HTML to separate folder
mkdir -p "$HOME/Desktop/conversation-showcase/html"
# HTML_PATH = $HOME/Desktop/conversation-showcase/html/wrap-up-{DATE}-{TIME}.html

"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=2 \
  --window-size={width},{height} \
  --screenshot="$HOME/Desktop/conversation-showcase/wrap-up-{DATE}-{TIME}.png" \
  "file://$HTML_PATH"
```

### Step 6: Self-Check

Read the rendered PNG and verify:
1. Headline is clearly the biggest, most readable text
2. Session count badge ("今日 N 个 session") is visible near header
3. All task rows are fully visible, with soft backgrounds and good spacing
4. Metrics pills are colorful (pastel) and easy to read
5. Agent tags are visible
6. Commentary box is fully visible with cute robot icon and bubble
7. Footer "✦ {Platform}" is visible
8. No text cutoff anywhere
9. **Vibe check**: does it feel warm, friendly, 小红书-ready? Or cold and techy? If the latter, adjust.
10. **Phone readability test**: imagine viewing this at 375px wide — can you still read the headline and tasks?

If any issue: adjust content/sizing, re-render, re-check.

### Step 7: Output

Report to user:
- Theme used
- Number of sessions scanned and summarized (e.g., "扫描了今日 3 个 session")
- The headline generated
- Number of tasks and agents summarized
- The commentary line (so they can approve or request a different one)
- File path

## Branding

- Header: `✦ {Platform} · 今日收工` (auto-detect: Claude Code / Gemini CLI / OpenCode / Cursor / etc.)
- Footer: `✦ {Platform}`
- Commentary label: `✦ Agent Says`
- Agent icon: cute round robot face (CSS/SVG rendered, not ◠‿◠)
- Fallback platform name if undetectable: `AI Coding Agent`
- By JORAN 觉然
