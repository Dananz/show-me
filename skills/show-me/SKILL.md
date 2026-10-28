---
name: show-me
description: "Visual mode for T3 Code. Answers become short inline HTML renders (html_preview + html_render) with Phosphor icons, clear color meaning, card sections and flowcharts when there is a flow. Code never goes inside HTML: renders stack above the reply, and code goes in the final reply as fenced blocks linked to the renders by numbered badges. Use on /show-me, $show-me, \"visual mode\", \"show me\", \"draw it\". Stays on for the whole session until \"stop visual mode\" or \"normal mode\"."
---

# show-me

The reader is a visual learner. They get a picture faster than a paragraph. So answers are small inline HTML renders: icons, color that means something, clear sections, flowcharts when something flows. Text is only for code and a one-line next action.

## Persistence

These rules apply to every reply for the rest of the session, not just this one. They don't expire after a few turns and don't lapse when the topic changes. If you're unsure whether they still apply, they do.

Turn them off only on "stop visual mode" or "normal mode". Confirm that in one line, then go back to plain replies.

## What visual learning changes

1. **A paragraph is a wall.** The eye skips it. A card with an icon, a title and one sentence gets read.
2. **Color is read before words.** Green, amber and red register before the label does, so color must always mean the same thing.
3. **Flows live in space.** "A calls B, which then..." needs to be read twice. Boxes and arrows need one look.
4. **Grouping is meaning.** Things in the same card belong together. Separate cards are separate ideas.
5. **A picture without words is a puzzle.** Short is good, cryptic is not. Fragments like "orderId body, returns empty 200" force the reader to decode. Someone who wasn't following the session must understand the first render.
6. **Code is text, not a picture.** It has to be copyable and syntax-highlighted. T3 already does that well in fenced blocks.

## How (the tools)

The renders use two tools from T3 Code's MCP server (`t3-code`): `html_preview` and `html_render`. Any agent T3 Code runs (Claude, Codex, Antigravity, Cursor, OpenCode, Pi...) gets them. How to reach them depends on the agent:

- **Claude Code:** they are deferred. Load them once per session with `ToolSearch`, query `select:mcp__t3-code__html_preview,mcp__t3-code__html_render`.
- **Codex:** they are loaded already, as `mcp__t3_code__html_preview` and `mcp__t3_code__html_render`.
- **Other agents:** call them from the `t3-code` MCP server directly. If they are not visible, search your tool list for `html_render`.

1. Write the HTML. Start from `template.html` next to this file.
2. Call `html_preview`. Check the screenshot. Fix and re-preview if anything is clipped or broken.
3. Call `html_render` with `height` = the preview's `contentHeight` and a short `title`.
4. Don't announce or restate the render in text. The picture is the answer.

If `html_render` doesn't exist (not in T3 Code), fall back to markdown: a status line, a table, and short headed sections.

## Rules

### 1. Lead with the result, in color, then one plain sentence

The first render opens with the answer: one status row (icon + color + a few words). Right under it, a **context line**: 1 to 2 full sentences in plain language saying what this is, where we are, and what (if anything) the reader needs to do. This line is not optional. Without it the cards are a puzzle.

Bad: a card titled "Background" that explains the auth flow.
Good: a green `ph-check-circle` "Login works with magic links", then cards for what changed.

### 2. Code leaves the HTML

Never put code, commands, diffs, config or logs inside a render (no `<pre>`, no code blocks). Code goes in the final text reply as fenced blocks.

T3 shows every render of a turn stacked above the final reply. Text written between tool calls does not appear between renders (tested 2026-10-28). So render, code, render in one message is impossible. Instead, link the two with numbered badges:

```
[render 1: overview, step cards with badges ① ② ③]
[render 2: flow / options, if needed]
final reply:
  ① Install        + fenced block
  ② Add the route  + fenced block
  next action
```

- In the render, a step that has code gets a small badge (`①`, `②`) and a `ph-code` icon. That says "the code is below".
- In the final reply, each code block opens with a one-line label using the same badge number. No prose around it.
- Inline names inside a sentence (a file, a flag) are fine in HTML as `<code>` with the mono font.

### 3. Flows get a flowchart

If the answer has order, branches, or "A then B", draw it.

- Linear, 3 to 5 steps: a row of step cards joined by `ph-arrow-right`. No library.
- Branches or loops: Mermaid with theme variables (see `template.html`). At most 8 nodes, `flowchart LR` (`RL` in Hebrew). Avoid `TD`: on a phone it grows to ~700px tall, while `LR` scales down (its text gets small, so keep it to 6 nodes when the answer may be read on a phone).

Bad: "First the request hits middleware, which checks the token, and if it's invalid it redirects..."
Good: `Request → Middleware → [token ok?] → yes: Handler / no: /login`, as a chart.

### 4. Color always means the same thing

Always use theme variables, never hex.

| Meaning | Variable | Icon |
|---|---|---|
| Done, works, recommended | `--success` | `ph-check-circle` |
| Caution, tradeoff, watch | `--warning` | `ph-warning` |
| Broken, risk, don't | `--destructive` | `ph-x-circle` |
| Info, neutral fact | `--info` | `ph-info` |
| Categories, series, steps | `--chart-1` to `--chart-6` | per topic |

No color for decoration only. If it's colored, it means something.

### 5. Cards, not paragraphs

- A section is a card: `bg-[var(--card)] border border-[var(--border)] rounded-[var(--radius)] p-4`.
- Each card has one Phosphor icon, a short title, and one or two **full sentences** (subject + verb). Write "The app sends the order id and gets an empty 200 back", not "orderId body, empty 200".
- Each group of cards gets a heading that says what the group means ("2 new endpoints the app will call"), not a bare path or label floating above it.
- Every icon must say something different. Three cards with the same `ph-code` icon is decoration: use the icon of the thing (`ph-file-ts`, `ph-plug`, `ph-shield-check`) or drop it.
- No empty space for its own sake: a card's height comes from its content.
- Big numbers are big (`text-2xl font-semibold`). Labels are small and muted (`text-xs text-[var(--muted-foreground)]`).
- 2 to 4 cards per row (`grid-cols-2`/`3`). Long text stacks in one column.

### 6. Plain words before jargon

The reader may not know the codebase's internal names. Name things by what they are first, the technical name second:

- Bad: "FE calls `/coupon?orderId`, returns eligible + expired"
- Good: "The app asks whether this order can take a coupon (`GET /coupon`). It gets back: is it allowed, and has the offer expired."

- Abbreviations (FE, DTO, PR, CI) get spelled out or replaced the first time: "the app", "the request shape".
- Status codes and numbers carry their meaning: "404 (not found)", "200, empty body", not a bare "404".
- A decision or design choice says why, in a "so that" clause: "The user comes from the session, so no one can touch another user's order."

### 7. Cap groups at 5

At most 5 items in a row, list or group. More than that means rank them and group them; show the rest when asked. This shapes presentation only. Never drop relevant items when completeness matters.

### 8. Multi-step work: number it and show the state

Steps are numbered cards, each one bounded action. Every reply in multi-step work shows where we are: a progress row (done = `--success` filled, current = `--primary` ring, next = muted). The reader shouldn't have to remember "step 3 of 5".

Every step in the progress row has a name, not just a number. "1 ✓ 2 ✓ 3 ✓" tells nothing. If names don't fit, use a vertical list.

### 9. Make wins visible

Completed work is a green card that says what now works, concretely. Don't bury it in a recap.

Bad: "Made some changes to the auth flow, among other things..."
Good: green card "Magic link login works" + "Try: open `/login`".

### 10. Errors are matter-of-fact

A red card with three rows: **Cause**, **Fix**, **Where** (`file:line`). No "uh oh", no drama. The fix code goes in a fenced block after the render.

### 11. Options are a comparison

"What are my options" gets 2 to 4 option cards side by side. Recommended first, with a `--success` "Recommended" tag. Each one has a single pro row and a single con row.

### 12. Questions go through the ask tool, never a card

If the reader must decide something, always ask with the agent's question tool: `AskUserQuestion` in Claude Code, `request_user_input` in Codex, the equivalent elsewhere. Never render a question card and wait for a typed answer. Only if the agent has no such tool, ask in plain text with the choices numbered.

The dialog may be all the reader sees while it is open, so each question carries its full detail:

- The question states the context in plain words: what this is about, what you found, and why it needs their call. Two or three sentences are fine.
- Every option gets a description with what it means and what happens if chosen. Recommended option first, marked "(Recommended)", with the reason in its description.
- Internal names follow rule 6: the plain meaning first, then the name (`ID_UPLOAD`, a file path, a date).
- Several open questions go in one call (up to 4), not one card listing "a." and "b.".

A render before the ask can still show the situation (what was found, a flow), but the decision itself lives in the tool. Never tuck a decision into a warning card's subtitle.

For something the reader must *do* (not decide), like "run this command", use a card with `ph-hand-pointing` and the action spelled out.

### 13. Specific time estimates

If it costs time, put a concrete number on the card ("~15 min", "an afternoon"), not "a bit of work".

### 14. No preamble, no recap, no closer

No "Here's a visual overview" before a render. No text restating what the render shows. No "Let me know if...". End with one plain-text line: the one next action, doable in under two minutes.

## Frame rules (from T3)

- No background, border or padding on `html`, `body` or the outer element. No banner title.
- No `vh` or `height:100%`. Content sets the height. Charts get fixed pixel heights.
- Theme variables only: `--background --foreground --muted --card --border --primary --accent --destructive --warning --success --info --chart-1..6 --radius --font-sans --font-mono` (most have `-foreground`).
- Desktop width is 728px, phones are about 360px. Grids should wrap (`grid-cols-[repeat(auto-fit,minmax(160px,1fr))]`). T3 measures the height per width by itself, so one `height` from the 728px preview is enough.
- Tables: wrap in `overflow-x-auto`, and give long identifiers `[overflow-wrap:anywhere]`. More than 3 columns on a phone means cards instead of a table.
- Light mode works with the same variables. No extra work, just never hardcode colors.
- Language follows the reply. Hebrew content (tested 2026-10-28):
  - `<html dir="rtl" lang="he">`, `ph-arrow-left` instead of `ph-arrow-right`, Mermaid `flowchart RL`.
  - Inline code and paths get `dir="ltr"` so they don't flip.
  - A long path or identifier never sits mid-sentence in Hebrew (the sentence order breaks). Give it its own row: icon + `<code dir="ltr">` on one line, the Hebrew line under it.
  - No `~` before numbers (bidi moves it to the wrong side). Write `כ-25 דק׳`.
  - Code labels in the final reply are in Hebrew too; the code stays as is.

## Shape of a visual reply

1. **Render 1:** the result at a glance (status row + up to 5 cards). Steps that have code get badges ①②③.
2. **Render 2** (only if needed): flow, options, or progress.
3. **Final reply text:** the code blocks, each labeled with its badge. Nothing else.
4. **Last line, plain text:** the one next action.

All renders sit above the text, whatever order the tools were called in.

Length follows the content. One or two renders is typical. If an answer needs more, split it into more renders by topic rather than squeezing it into fewer cards. Cut repetition and filler, never information the reader needs to act or decide.

## When to break the rules

1. **Tiny answers.** Yes/no, a one-liner, a quick confirm: plain text, no render. A render for one fact is noise.
2. **The answer is code.** "Write me a function" is mostly a fenced block. Add a render only if there's a flow or a choice worth seeing.
3. **"Explain" or "walk me through".** More renders are allowed (up to 4), one per concept, each with a heading-like status row so the reader can skim back.
4. **Destructive action ahead** (`rm -rf`, force push, migration, dropping data). A red confirm card before acting. Safety beats format.
5. **Debug spiral.** After three "still broken" turns, stop iterating. Render one card: the assumption that might be wrong + one diagnostic question.
6. **The harness wins.** The system prompt outranks this skill. Same principle: the constraint wins, the shape stays.

## Pre-send check

1. Can the reader get the point from the first render alone?
2. Is any code inside HTML? Move it to a fenced block in the final reply, with a matching badge in the render.
3. Is any color hex, or decoration only? Fix it.
4. The newcomer test: would someone who wasn't following understand the first render? If a card is a fragment, rewrite it as a sentence. If a card runs past two sentences, split it into two cards.
5. Is there an internal name, abbreviation or bare number with no plain-language meaning next to it? Add the meaning.
6. Is there text before or after a render that restates it? Delete it.
7. Does the last line name one concrete next action?
8. Is there a question inside a render? Move it to the ask tool, with full context in the question and a description on every option.
