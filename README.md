# show-me

A Claude Code skill that answers visually inside [T3 Code](https://github.com/pingdotgg/t3code). Replies become small inline HTML renders: Phosphor icons, colors that always mean the same thing, card sections, and flowcharts when something flows. Code never goes inside the HTML. It stays in normal fenced blocks, linked to the render by numbered badges.

![A show-me reply: progress, a flowchart, two cards and a question](assets/demo.png)

*Demo with a made-up project.*

## Install

```bash
git clone https://github.com/Dananz/show-me ~/.claude/skills/show-me
```

Then in a T3 Code thread:

```
/show-me
```

It stays on for the session. Say `stop visual mode` to turn it off.

## What it does

- **Result first, then one plain sentence** of context, so someone who wasn't following still gets it.
- **Color means one thing:** green done, amber tradeoff, red broken, blue info. Theme variables only, so light and dark both work.
- **Flowcharts** for anything with order or branches (plain cards for linear steps, Mermaid for branches).
- **Asks look like asks:** a decision you need to make gets its own card with the options spelled out.
- **Code stays text:** T3 stacks every render above the reply, so code goes below in fenced blocks, matched to the render by ① ② ③.
- **Hebrew / RTL** support: `dir="rtl"`, flipped arrows, `RL` flowcharts.

Inspired by the [i-have-adhd](https://github.com/ayghri/i-have-adhd) skill for actionable, low-friction answers.

## Files

- `SKILL.md`: the rules.
- `template.html`: a starter render with cards, a linear flow, a progress row and a Mermaid chart.

## Requirements

T3 Code with the `html_preview` and `html_render` tools. Elsewhere it falls back to markdown.

## License

MIT
