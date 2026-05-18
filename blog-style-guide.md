# Zhipeng's Blog Writing Style Guide

## Voice & Tone

- **Conversational, not academic.** Write like you're explaining something cool to a smart friend over coffee — not presenting a paper.
- **First-person narrative.** Every post starts from a personal experience ("I asked an AI to...", "I was working on an assignment..."). The reader follows your discovery journey.
- **Curious, not authoritative.** Frame yourself as someone who found something interesting and dug into it — not as an expert lecturing from above.
- **Simple language, real depth.** Avoid jargon where possible; when technical terms are necessary, define them immediately with plain-English analogies.

## Structure

Every post follows this skeleton:

```
1. Hook          — One or two sentences. A surprising observation or question.
2. Personal story — "I was doing X when I noticed Y." Real code, real assignment, real experience.
3. The core question — Extract the deeper "why" from the story.
4. Explanation    — Break down the concept. Use analogies, diagrams, code, tables.
5. Broader context — How does this connect to the bigger picture? What do researchers say?
6. Solutions      — Practical methods to address or work around the problem.
7. Key Takeaways  — Numbered list, bolded key phrases, one sentence each.
8. Closing line   — A single punchy, italic, centered sentence. Philosophical. No fluff.
9. Resources      — Curated links: papers, tools, videos, tutorials.
```

## Opening

- **First sentence is a hook** — something counterintuitive, a question, or a vivid scene. Never start with a definition.
- **Second paragraph is the personal story** — ground the topic in your real experience (CS146S coursework, side projects, etc.).

Examples:
> "Have you ever asked ChatGPT to reverse a word and gotten a wrong answer?"
> "I asked an AI to solve a math problem. It wrote Python code, 'ran' it, showed intermediate results, and arrived at the correct answer. Impressive — until I looked closer."

## Explaining Concepts

- **Analogy first, definition second.** ("Think of it as the 'atom' of language for AI.")
- **Show, don't just tell.** Use code blocks, before/after comparisons, concrete examples with real inputs and outputs.
- **One concept per section.** Don't overload a section with multiple new ideas.
- **Use "you" and "we" freely.** ("When you type 'httpstatus', the AI doesn't see...")
- **Bold key terms** on first appearance, then use them naturally after.

## Code

- **Always real, runnable code** — from actual assignments or working examples.
- **Python by default.** Keep it simple: no unnecessary imports, no boilerplate.
- **Annotate with comments** when the logic isn't obvious.
- **Show input → output** when demonstrating a point.

## Visual Elements

- **SVG diagrams** for process flows, comparisons, and architecture illustrations. Store in `/assets/images/blog/`.
- **Tables** for structured comparisons (tokenizer types, model specs, etc.).
- **Code blocks with results** — show the code AND what it produces.
- **Interactive tool links** — always link to tools the reader can try themselves (e.g., Tiktokenizer).

## Closing Line

- One sentence. Centered. Italic. Larger font (`1.4em`).
- Philosophical or reflective — not a summary, not a call-to-action.
- Reveals something about the gap between human and machine cognition.

Format in markdown:
```html
<p style="font-size: 1.4em; font-style: italic; text-align: center; margin: 2rem 0;">
Your closing line here.
</p>
```

Examples:
> "AI doesn't misunderstand language — it never saw it the way we do."
> "The best liars don't get the facts wrong — they get the reasoning wrong."

## Resources Section

- Title: `## Useful Resources`
- Format: markdown links, one per line, with descriptive titles.
- Include a mix of: academic papers, practical tools, YouTube videos, blog posts.
- Only link things you actually referenced or found valuable while writing.

## Front Matter Template

```yaml
---
layout: post
title: "Short, Punchy Title: Subtitle With More Detail"
date: YYYY-MM-DD
tags: [AI, LLM, topic1, topic2, tutorial]
lang: en
subtitle: "One-line description that tells the reader what they'll learn"
cover_image: /assets/images/blog/your-diagram.svg
---
```

## What NOT To Do

- Don't start with a definition ("Tokenization is the process of...")
- Don't write walls of text without code, diagrams, or examples
- Don't use buzzwords without explaining them
- Don't be preachy or moralize — stay curious
- Don't pad sections — if a point is made, move on
- Don't include resources you didn't actually use
