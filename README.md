# show-me

A Claude Code skill that answers visually inside [T3 Code](https://github.com/pingdotgg/t3code). Replies become small inline HTML renders: Phosphor icons, colors that always mean the same thing, card sections, and flowcharts when something flows. Code never goes inside the HTML. It stays in normal fenced blocks, linked to the render by numbered badges.

![A show-me reply: progress, a flowchart, two cards and a question](assets/demo.png)

*Demo with a made-up project.*

## Install

In Claude Code (2.1.275 or later):

```
/plugin install show-me --marketplace Dananz/show-me
```

Older versions, two steps:

```
/plugin marketplace add Dananz/show-me
/plugin install show-me@dananz
```

Then in a T3 Code thread:

```
/show-me:show-me
```

It stays on for the session. Say `stop visual mode` to turn it off.

### Updates

Every push to `main` is a new version. To get updates automatically, open `/plugin`, go to **Marketplaces**, pick `dananz` and choose **Enable auto-update**. Otherwise run `/plugin marketplace update dananz`.

## What it does

- **Result first, then one plain sentence** of context, so someone who wasn't following still gets it.
- **Color means one thing:** green done, amber tradeoff, red broken, blue info. Theme variables only, so light and dark both work.
- **Flowcharts** for anything with order or branches (plain cards for linear steps, Mermaid for branches).
- **Asks look like asks:** a decision you need to make gets its own card with the options spelled out.
- **Code stays text:** T3 stacks every render above the reply, so code goes below in fenced blocks, matched to the render by ① ② ③.
- **Hebrew / RTL** support: `dir="rtl"`, flipped arrows, `RL` flowcharts.

Inspired by the [i-have-adhd](https://github.com/ayghri/i-have-adhd) skill for actionable, low-friction answers.

## Files

- `plugins/show-me/skills/show-me/SKILL.md`: the rules.
- `plugins/show-me/skills/show-me/template.html`: a starter render with cards, a linear flow, a progress row and a Mermaid chart.
- `.claude-plugin/marketplace.json`: the marketplace that lists the plugin.

## Requirements

T3 Code with the `html_preview` and `html_render` tools. Elsewhere it falls back to markdown.

## License

MIT
