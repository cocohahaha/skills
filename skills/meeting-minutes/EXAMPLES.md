# Examples: Before & After

This document demonstrates how the five anti-error rules prevent speaker attribution failures. All examples are from the DLG onboarding + AI meeting transcripts used to develop this skill.

---

## Example 1: CLI vs VS Code — The Classic Swap

### Original Transcript (mario.txt L152-183)

```
说话人 2: "I never use cursor, terminal call code, use command line tools...
           I don't even see the code."
说话人 1: "my problem with the terminals... if I close the terminal...
           it's never to the last point... So that's why I started using VS"
```

**Fact:** 说话人 2 (new hire) uses CLI. 说话人 1 (Mario) prefers VS Code.

### ❌ Third-Person (Typical DeepSeek Output)

> Mario 是 CLI 原教旨主义者，只用终端和命令行工具，"I don't even see the code"。新成员则偏好 VS Code 的图形界面。

**Error:** Swapped the two speakers completely. This is the most common failure mode.

### ✅ First-Person (This Skill's Output)

> 我和 Mario 聊了各自的开发习惯。我用的是 Claude Code 这类 CLI 工具，直接在终端里用自然语言操作。Mario 说他以前也用终端，但有个问题——关掉终端就丢了上下文，所以他转向了 VS Code。

**Why it works:** The narrator is always "我" (the participant). No need to decide "who said what about tools" — the card simply reports what "I" use and what "I learned about Mario's preference."

---

## Example 2: "Fire in the Building" — Anxiety Attribution

### Original Transcript (mario.txt L24-29)

```
说话人 2: "I feel like I'm in a building that has fire already...
           when I got the offer for this place, it feels like I moved
           to another room. But the fire one day will come to this room."
说话人 1: "the world changes and might as well know how to use it
           as much as we can"
```

**Fact:** 说话人 2 (new hire) expressed AI anxiety. 说话人 1 (Mario) responded with calm pragmatism.

### ❌ Third-Person

> Mario 坦言自己深陷 AI 焦虑："我感觉自己在一个已经着火的楼里……"这种焦虑驱动他持续学习。

**Error:** Attributed the anxiety to Mario, reversing the emotional dynamic of the conversation.

### ✅ First-Person

> 我说了自己对 AI 替代的焦虑——感觉像是在一个已经着火的楼里，只是换了个房间，但火迟早会烧过来。Mario 的回应倒是很淡定：世界在变，不如尽量学会用它。

**Why it works:** "我" owns the anxiety. Mario's response is reported as what "I heard from him." The emotional dynamic is preserved because the narrator cannot accidentally swap perspectives.

---

## Example 3: RAG Architecture — Safe Attribution

### Original Transcript (mario.txt L554-563)

```
说话人 1: "every AI is as smart as the input it takes.
           So if I have a very strong database..."
```

**Fact:** 说话人 1 (Mario) said this about why the RAG knowledge base matters.

### ❌ Third-Person

> 新成员指出："AI 的聪明程度等于输入的质量，所以强大的数据库是一切的基础。"

**Error:** Attributed Mario's core insight to the new hire.

### ✅ First-Person

> Mario 说了一句让我印象很深的话——"AI 的聪明程度等于输入的质量。"这解释了他为什么把 RAG 知识库看作整个平台最重要的地基。

**Why it works:** "Mario 说了一句让我印象很深的话" — the attribution is explicit and verifiable. The narrator is reporting what they heard, not claiming the insight as their own.

---

## Example 4: Content to Omit — Tool Preference Debate

### Original Transcript (mario.txt L150-190)

A rapid back-and-forth about Cursor vs Windsurf vs terminal tools, anti-gravity IDE, etc. Speaker labels toggle every 1-2 lines.

### ❌ Included in Summary

> 团队讨论了多种开发工具：Cursor、Windsurf、Anti-gravity（Google 的 IDE）、Claude Code CLI。Mario 认为……

**Error:** High risk of misattribution due to rapid speaker switching. Zero information value for meeting outcomes.

### ✅ Omitted (Rule 3)

This entire segment is skipped. It contains no decisions, no project information, and no action items. It's a casual tool preference chat between engineers. The card series loses nothing by omitting it.

---

## Example 5: Multi-Session Structure — Source-Based Organization

### Scenario

One transcript file contains three back-to-back sessions:
1. Momo (HR) — account setup, office tour, policies
2. Tina (Ops Director) — AI platform access, org chart, onboarding plan
3. Mario (AI Engineer) — AI Hub demo, RAG knowledge base, workflow vision

### ❌ Topic-Based Structure (Typical)

```
Card 1: 公司 AI 工具栈
Card 2: 人事制度
Card 3: 关键人物介绍
Card 4: AI Hub 平台架构
```

**Problem:** Card 1 mixes Tina's AI platform info with Mario's AI Hub info. Card 3 mixes Momo's introduction of Mario with Tina's introduction of Elsie/Olivia/Roni. Information sources are tangled.

### ✅ Source-Based Structure (This Skill)

```
Card 1: Cover — 今天的三场对话
Card 2: Momo 帮我做的设置 — 邮箱/企微/VPN/字体/合规文档
Card 3: Momo 讲的制度 — 工时/加班/年假/报销/居家办公
Card 4: Tina 开的 AI 权限 — Claude/GPT/Notta/即梦 账号
Card 5: Tina 介绍的关键人物 — Elsie/Olivia/Roni 及其职责
Card 6: Mario 展示的平台 — AI Hub 是什么、为什么做、怎么跑
Card 7: Mario 讲的核心思路 — RAG 知识库为什么是地基
Card 8: Mario 的自动化愿景 — Brief → 内容 → 报告全链路
Card 9: 我的下一步 — 见人/产出/评估/阅读
```

**Why it works:** Each card has a single, named information source. Even if the model confuses two speakers within a session, the card-level attribution remains correct. And because each card uses "我" narration, internal statements don't need speaker labels at all.

---

## Summary: Why First-Person Works

| Dimension | Third-Person (risky) | First-Person (safe) |
|-----------|---------------------|---------------------|
| Speaker identification | Required for every statement | Not needed — narrator is always "我" |
| Error blast radius | One wrong attribution undermines entire card | Error contained to one reported observation |
| Omission decision | Hard — "who said this?" affects whether to include | Easy — "did I learn something useful?" is the only filter |
| Emotional accuracy | Requires correctly mapping emotions to speakers | Emotions belong to "我" by default, others' are explicitly reported |
| Cross-model reliability | Varies dramatically (Opus > Sonnet > DeepSeek) | Consistent — narrative constraint is architecture-level |
