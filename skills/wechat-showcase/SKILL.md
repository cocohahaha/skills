---
name: joran-wechat-showcase
description: "Generates shareable social media infographics from WeChat conversation data by JORAN 觉然. Reads WeChat chat history, curates highlights, and produces a 2-4 page PNG series (Cover + Highlights). Focus on celebrating friendships through curated conversation excerpts — no data dashboards. Use when user asks to \"wechat showcase\", \"微信展示\", \"聊天分析\", \"对话分析\", or wants to visualize a WeChat friendship."
---

# WeChat Showcase

Transform WeChat conversation history with a specific contact into a beautiful infographic series (2-4 pages, scaled to data volume). Focus on curated conversation highlights — no data dashboards.

## IMPORTANT: Always Ask First

**Before doing ANYTHING, you MUST ask the user TWO questions:**

```
请告诉我：
1. 你想看和谁的聊天记录？（联系人昵称或备注名）
2. 时间范围是多久？（例如：最近30天 / 最近3个月 / 全部）
```

**Do NOT proceed until the user answers both questions.** These are required parameters — there are no defaults.

## Usage

```bash
/wechat-showcase                    # Will prompt for contact + time range
/wechat-showcase 素素 30天          # Direct: contact "素素", last 30 days
/wechat-showcase 大福 全部          # Direct: contact "大福", all messages
```

## Options

| Option | Values | Default |
|--------|--------|---------|
| contact | Contact name (required) | **Must ask user** |
| days | Number or "全部" (required) | **Must ask user** |
| `--theme` | `warm`, `cool`, `minimal` | `warm` |
| `--aspect` | `xhs` (1080x1440), `square` (1080x1080) | `xhs` |

## Dynamic Page Count

Page count scales with message volume (Cover + Highlights only, no data dashboards):

| Messages | Pages | Structure |
|----------|-------|-----------|
| < 50 | 2 | Cover + Highlights |
| 50-200 | 3 | Cover + Highlights P1 + Highlights P2 |
| 200-1000 | 4 | Cover + Highlights P1 + P2 + P3 |
| 1000+ | 5 | Cover + Highlights P1 + P2 + P3 + P4 |

## Content Curation Guidelines

When selecting conversation excerpts for Highlights pages, apply these principles:

**DO extract:**
- Creative work discussions (how user makes things with AI, music, code, art)
- Genuine funny/witty banter between friends
- Collaborative ideas and brainstorming
- Emotionally supportive or heartfelt moments
- Interesting tech insights or discoveries shared between friends

**DO NOT extract:**
- Content that mocks, attacks, or negatively judges specific people or groups (e.g., making fun of someone's appearance or career struggles)
- Content that creates or amplifies anxiety (AI replacing jobs panic, doom scenarios)
- Mundane logistics ("到了吗" "好的" "在哪")
- Private/sensitive information (bank details, passwords, personal addresses)
- Pure emoji/sticker exchanges with no text substance
- **Content that could trigger China mainland social media platform violations:** mentions of cross-border payments/transactions (交易、转账、货币), VPN/circumvention tactics (转区、注册海外ID), directing users to private messages for deals (私信、需要的话可以发你), or anything that could be flagged as marketing/solicitation by platform moderation

**Tone principle:** The showcase should celebrate the friendship and make both parties look good. If an excerpt is funny at someone else's expense, skip it.

## Output

```
~/Desktop/wechat-showcase/
├── {contact}-showcase-{DATE}-01.html    # Cover (always)
├── {contact}-showcase-{DATE}-02.html    # Highlights P1 (always)
├── {contact}-showcase-{DATE}-03.html    # Highlights P2 (500+ msgs)
├── {contact}-showcase-{DATE}-04.html    # Highlights P3 (2000+ msgs)
└── *.png                                 # Rendered images (one per HTML)
```

Page indicator dots at the bottom of each page reflect the actual page count (e.g., 3 pages = 3 dots, with the current page filled).

## Workflow

### Step 0: Ask User (MANDATORY)

Use AskUserQuestion to get:
1. **Contact name** — who to analyze
2. **Time range** — how far back to look

### Step 1: Database Access (MCP-first, direct fallback)

#### Primary: WeChat MCP Tools

The WeChat MCP server is configured in Claude Code settings (`~/.claude/settings.json` under `mcpServers.wechat`). Always try MCP tools first.

**Available MCP tools and how to call them:**

1. **`mcp__wechat__wechat_list_chats()`** — List all conversations with nicknames and remarks.
   - No arguments required
   - Returns: list of contacts with nickname, remark, and wxid
   - Format: `昵称 (备注: xxx) (wxid: xxx)`

2. **`mcp__wechat__wechat_read_chat(contact, limit, days)`** — Read messages with a specific contact.
   - `contact` (str, required): wxid, nickname, or remark (supports fuzzy match)
   - `limit` (int, default 50): max messages to return
   - `days` (int, default 7): how many days back to read
   - Returns: timestamped messages with **direction markers: `[我]` = user sent, `[对方]` = contact sent**
   - **For showcase, set `limit` high (e.g., 2000-5000) and `days` to match user's time range**

3. **`mcp__wechat__wechat_search_messages(keyword, days, limit)`** — Search messages by keyword.
   - `keyword` (str, required): search term
   - `days` (int, default 30): search window
   - `limit` (int, default 50): max results

4. **`mcp__wechat__wechat_recent_messages(days, limit)`** — Overview of recent messages across all chats.
   - `days` (int, default 3): how many days back
   - `limit` (int, default 100): max messages per conversation

5. **`mcp__wechat__wechat_chat_summary(days)`** — Structured summary for analysis.
   - `days` (int, default 3): analysis window

**Recommended MCP workflow:**
```
1. Call wechat_list_chats() to find the contact (now shows nicknames)
2. Call wechat_read_chat(contact="昵称或备注", limit=5000, days=<user_range>)
   to get the full message history — you can search by nickname/remark directly
3. Messages include [我]/[对方] direction markers — use these for chat bubble layout
4. If you need more context, call wechat_search_messages() for specific keywords
```

#### Fallback: Direct SQLCipher Access

Only use this if MCP tools are unavailable (server not running, tools return errors).

**Database location:**
```
~/Library/Containers/com.tencent.xinWeChat/Data/Documents/xwechat_files/*/db_storage/
```

**Decryption:** Databases are SQLCipher encrypted. Use the key from:
```
~/Downloads/试验/ai 101/wechat-mcp-server/../wechat-decrypt-macos/key.txt
```

**Direct query via sqlcipher CLI (`/opt/homebrew/bin/sqlcipher`):**
```bash
/opt/homebrew/bin/sqlcipher <db_path> <<'SQL'
PRAGMA key = "x'<key_from_file>'";
PRAGMA cipher_compatibility = 4;
PRAGMA cipher_page_size = 4096;
.headers on
.mode csv
SELECT create_time, local_type, message_content FROM Msg_<md5_of_wxid>
WHERE create_time > <unix_timestamp> ORDER BY create_time;
SQL
```

### Step 2: Data Analysis

After retrieving messages, determine the total message count to decide page count:
- **< 100 messages** -> 2 pages (Cover + Highlights)
- **100-500 messages** -> 2 pages (Cover + Highlights)
- **500-2000 messages** -> 3 pages (Cover + Highlights P1 + Highlights P2)
- **2000+ messages** -> 4 pages (Cover + Highlights P1 + Highlights P2 + Highlights P3)

Calculate these basic metrics (for Cover page only):

1. **Basic stats:** Total messages, active days/hours, date range
2. **Emotional density score (0-100):**
   ```
   Volume(20) + Emoji(15) + LateNight(15) + Consistency(25) + Depth(25)
   ```

Then focus on **curating conversation excerpts** per the Content Curation Guidelines above. This is the core of the showcase — spend most effort here selecting the best exchanges.

### Step 3: Generate HTML Pages

Use the template at `references/template.html`. Generate pages based on tier:

#### Page 1 — Cover (all tiers)
Contact name + key stats + emotional density score + page dots (bottom)

#### Highlights Pages (all tiers, starting from Page 2)
- **< 50 msgs:** 1 highlights page with 2 conversation excerpts
- **50-200 msgs:** 2 highlights pages with 2 excerpts each, grouped by theme
- **200-1000 msgs:** 3 highlights pages with 2 excerpts each, grouped by theme
- **1000+ msgs:** 4 highlights pages with 2 excerpts each, grouped by theme

Each excerpt should have 3-4 bubbles of back-and-forth — enough context to feel the conversation's rhythm, not just a punchline.

**Layout rules for Highlights pages:**
- **No section titles or subtitles** on highlights pages — let the chat bubbles speak for themselves. The timestamp dividers between excerpts provide enough structure.
- **Header brand** ("✦ WeChat Showcase") should be subtle: use smaller font (20px), lighter color, and minimal visual weight. It's a watermark, not a banner.
- **Minimize top whitespace** — go straight from header to chat content with minimal gap.
- **Chat container should not stretch** beyond its content — use `flex:0 1 auto` instead of `flex:1` to avoid large empty white areas below the last chat bubble. Let the page background fill the remaining space naturally.

Each excerpt is a chat bubble exchange (2-4 messages back-and-forth). Apply the **Content Curation Guidelines** when selecting excerpts — celebrate the friendship, skip anything that mocks others or creates anxiety.

**CRITICAL — Chat bubble layout on Highlights pages:**

Messages MUST display in **left-right alternating format**, like a real chat app:
- **`[对方]` messages** → left-aligned (with contact avatar on left)
- **`[我]` messages** → right-aligned (with user avatar on right)
- Messages alternate naturally, creating a back-and-forth visual rhythm

The MCP tool `wechat_read_chat` now returns each message with a `[我]` or `[对方]` prefix indicating direction. Use these markers directly to determine left/right placement — **no guessing needed**.

**Do NOT** put all messages left-aligned or stack them in a single card. Each message should be its own bubble with clear left/right positioning.

```html
<!-- Contact message (left) -->
<div class="chat-row chat-left">
  <div class="chat-avatar">T</div>
  <div class="chat-bubble chat-bubble-left">对方说的话</div>
</div>

<!-- User message (right) -->
<div class="chat-row chat-right">
  <div class="chat-bubble chat-bubble-right">我说的话</div>
  <div class="chat-avatar chat-avatar-user">我</div>
</div>
```

CSS for chat layout (add to template):
```css
.chat-row { display: flex; gap: 12px; align-items: flex-start; margin-bottom: 12px; }
.chat-right { flex-direction: row-reverse; }
.chat-avatar { width: 40px; height: 40px; border-radius: 50%; background: var(--border-strong); display: flex; align-items: center; justify-content: center; font-size: 14px; color: var(--text-muted); flex-shrink: 0; }
.chat-avatar-user { background: var(--accent); color: #fff; }
.chat-bubble { max-width: 70%; padding: 14px 18px; border-radius: 18px; font-size: 24px; line-height: 1.6; }
.chat-bubble-left { background: var(--surface-white); border: 1px solid var(--border); border-bottom-left-radius: 4px; }
.chat-bubble-right { background: var(--accent); color: #fff; border-bottom-right-radius: 4px; }
```

Separate different conversation excerpts with a timestamp divider:
```html
<div class="chat-divider">3月15日 23:42</div>
```
```css
.chat-divider { text-align: center; font-size: 16px; color: var(--text-muted); margin: 24px 0 16px; }
```

**Preventing content overflow (CRITICAL for PNG rendering):**

Use `height` (not `min-height`) and `overflow: hidden` on the card to ensure Chrome headless captures exactly the card area:
```css
.card {
  width: 100%;
  height: var(--card-height);  /* fixed height, NOT min-height */
  display: flex;
  flex-direction: column;
  padding: 56px;
  overflow: hidden;  /* prevent overflow from leaking out */
}
```
Since `overflow: hidden` will clip content that exceeds the card, you MUST ensure content fits during curation. If in doubt, use fewer excerpts or shorter bubbles.

#### Page Indicator Dots
Every page gets dots at the bottom reflecting total page count. Example for a 4-page series, page 2:
```html
<div class="page-dots">
  <span class="dot"></span>
  <span class="dot active"></span>
  <span class="dot"></span>
  <span class="dot"></span>
</div>
```

### Step 4: Render ALL Pages to PNG

Iterate over every HTML file generated and render each one:

```bash
OUTPUT_DIR=~/Desktop/wechat-showcase
for html_file in "$OUTPUT_DIR"/{contact}-showcase-{DATE}-*.html; do
  png_file="${html_file%.html}.png"
  "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
    --headless=new --disable-gpu \
    --screenshot="$png_file" \
    --window-size=1080,1440 \
    --force-device-scale-factor=2 \
    --hide-scrollbars \
    "file://$html_file"
done
```

Verify all PNGs were created.

### Step 5: Self-Check — Verify No Content Cutoff

**After rendering each PNG, you MUST visually inspect it by reading the PNG file with the Read tool.** Check for:

1. **Text cutoff** — any text clipped at the bottom or right edge of the card
2. **Overflow** — content extending beyond the card boundary
3. **Empty space** — large unused area at the bottom (page not filled enough)
4. **Overlapping elements** — messages or chat bubbles overlapping each other
5. **Chat alignment** — highlights pages must show left-right alternating bubbles (see Highlights format below)

**Detecting truncation (CRITICAL):**

1. Use the Read tool to view the rendered PNG file (Claude is multimodal and can see images)
2. Check that the **bottom** of the image contains BOTH of these elements:
   - **Page indicator dots** (the row of dots showing current page position)
   - **Footer** (the "✦ WeChat Showcase" text at the very bottom)
3. If either the page-indicator dots or the footer are missing/cut off, the PNG is truncated

**Fixing truncation:**

1. **Option A (preferred):** Reduce content — remove one excerpt, shorten bubbles, use fewer highlights
2. **Option B:** Increase `--card-height` CSS variable (e.g., from 1440px to 1540px) AND update Chrome `--window-size` height to match
3. After fixing, re-render the PNG and re-check
4. Repeat until both page-indicator dots and footer are fully visible

**This step is mandatory. Never deliver showcase images without visual verification.**

Report the final count to the user (e.g., "Generated 4 pages based on 1,200 messages").

## Themes

- **warm:** Clay accent (#d97757), cream background — intimate, personal
- **cool:** Blue accent (#6a9bcc), slate background — professional
- **minimal:** Black & white — clean, timeless

## Branding

- Header/Footer: `✦ WeChat Showcase`
- Tone: Celebrate connections, not transactions
