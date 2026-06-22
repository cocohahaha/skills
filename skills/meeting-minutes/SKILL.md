---
name: meeting-minutes
description: Summarize multi-speaker meeting transcripts into shareable HTML infographic cards. Uses first-person narration to avoid speaker attribution errors — critical for transcripts where speaker labels are unreliable. Use when the user needs meeting notes, client call summaries, project alignment docs, or cross-team sync outputs formatted as social-media-ready cards. Triggers include "会议总结", "meeting minutes", "整理会议", "通话纪要", "客户会议", "对齐会议", or any task involving transcript → infographic conversion.
---

# Meeting Minutes

Convert multi-speaker meeting transcripts into structured HTML infographic cards, optimized for internal sharing or Xiaohongshu (小红书). The core design principle: **first-person narration prevents speaker attribution errors** that plague third-person summaries of ambiguous transcripts.

## Why This Exists

Whisper/ASR transcripts label speakers as `说话人 1/2/3/4` with no identity mapping. Models summarizing in third-person ("X said… Y replied…") frequently swap speakers. This skill enforces a **limited-narrator** pattern that sidesteps attribution entirely — all information passes through "what I learned" rather than "who said what."

See [EXAMPLES.md](EXAMPLES.md) for a before/after comparison demonstrating the error patterns and how this skill prevents them.

## Quick Start

```
User: 帮我总结 /path/to/meeting.txt，输出为 HTML 卡片
User: 这是和甲方的项目对接会录音，帮我整理成纪要卡片
```

The skill accepts one or more transcript files and outputs an HTML card series.

## Workflow

### Step 1: Read & Identify Sessions

Read all provided transcript files. Identify distinct meetings/sessions within them. A single file may contain multiple back-to-back sessions (e.g., HR onboarding → ops briefing → tech deep-dive). Label each session by its **information source** — the person primarily delivering information to the participant.

### Step 2: Apply the Five Anti-Error Rules

**These rules are NON-NEGOTIABLE.** They exist because third-person summarization of ambiguous transcripts provably fails (see examples).

#### Rule 1: First-Person Only
Write EVERYTHING from the participant's perspective using "我" (Chinese) or "I" (English). Never use third-person narration ("新成员", "Mario said", "她认为"). The narrator IS the meeting participant.

```
✅ "我了解到公司的 AI 工具栈包括 Claude、GPT 和 Gemini"
❌ "Mario 向新成员介绍了公司的 AI 工具栈"
```

#### Rule 2: Structure by Source, Not by Topic
Organize cards by WHO briefed the participant, not by subject matter. Each card title names the source.

```
✅ Card 2: "Momo 帮我做的设置" → Card 3: "Tina 讲的制度" → Card 4: "Mario 展示的平台"
❌ Card 2: "工具栈" → Card 3: "制度" → Card 4: "平台"  (information sources mixed)
```

#### Rule 3: Selectively Omit
**Skip these categories entirely:**
- Casual banter and personal anecdotes unrelated to work
- Tool preference debates (CLI vs IDE, Mac vs Windows)
- Repeated information already covered in another card
- Any statement where you cannot determine the speaker with ≥90% confidence
- Compliments, small talk, meeting logistics ("can you hear me?" "let me share my screen")

#### Rule 4: Attribution Safety Net
Before finalizing each factual claim, ask: **"Do I know which speaker said this?"**

If uncertain:
- Rephrase as "我了解到..." or "会上提到..."
- Rephrase as "团队讨论认为..."
- **Delete it** if it's not essential

#### Rule 5: Dialogue → Monologue Conversion
Transcripts are dialogues. Cards are monologues. Convert exchanges into single-voice narration:

```
Transcript:
  说话人 1: "我们的 RAG 知识库是最核心的资产"
  说话人 2: "为什么？"
  说话人 1: "因为 AI 的聪明程度等于输入的质量"

Card output:
  "我理解了 RAG 知识库为什么是核心——AI 的聪明程度等于输入的质量。"
```

### Step 3: Determine Card Count & Content

Plan 5-9 cards based on information density:

| Meeting Length | Typical Cards | Structure |
|---------------|---------------|-----------|
| 30-60 min (single session) | 5-7 | Cover + 4-6 content cards |
| 60-120 min (multi-session) | 7-9 | Cover + 6-8 content cards |
| Half-day onboarding | 8-10 | Cover + 7-9 content cards |

Card types (mix as needed):
- **Cover card** — date, participants (by role, not name if uncertain), key theme
- **Setup/logistics card** — tools, accounts, access granted
- **Policy/rules card** — company policies, compliance requirements
- **People map card** — who's who, reporting lines, who to ask for what
- **System/architecture card** — platform overview, tech stack, data flow
- **Core insight card** — the "aha" moments, strategic takeaways
- **Workflow/vision card** — current process → target state
- **Next steps card** — action items, deadlines, upcoming meetings

### Step 4: Generate HTML Cards

Use the template at [TEMPLATE.html](TEMPLATE.html). Key visual specs:
- Card dimensions: **1080 × 1440px** (3:4 portrait, XHS-optimized)
- Color palette: cream background `#F4F1EA`, clay accent `#CC785C`, deep ink `#1A1916`
- Typography: serif (Songti/Georgia) for emphasis + sans-serif (PingFang) for body
- Each card is a self-contained `<section class="card">` element
- Output to a project subfolder: `meeting-notes/{DATE}-{TOPIC}/`

See [TEMPLATE.html](TEMPLATE.html) for the complete CSS and card markup patterns.

### Step 5: Self-Check Before Delivery

For each card, run this checklist:
1. ✓ Every sentence uses "我" perspective? (No "XXX说", "YYY认为")
2. ✓ Card title names the information source? (Not a topic label)
3. ✓ All quoted material can be attributed with certainty?
4. ✓ No tool preferences, banter, or ambiguous dialogue included?
5. ✓ Card count matches information density?

### Step 6: Render & Verify

Open the HTML in browser for visual review. If the user needs PNGs for XHS posting:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu \
  --screenshot="card-01.png" \
  --window-size=1080,1440 \
  --force-device-scale-factor=2 \
  "file://$(pwd)/meeting-notes/{DATE}-{TOPIC}/index.html"
```

## Multi-Model Note

This skill was designed after observing that DeepSeek and similar models frequently confuse speakers in multi-party transcripts. The first-person constraint is the single most effective intervention — it reduced attribution errors from ~15% to near zero in testing. When using this skill with any model other than Claude Opus, **follow the five rules exactly** — do not relax them for "better" models, as the error pattern is architectural (narrative mode), not capability-based.
