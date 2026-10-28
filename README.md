# show-me

An agent skill that answers visually inside [T3 Code](https://github.com/pingdotgg/t3code). Replies become small inline HTML renders: Phosphor icons, colors that always mean the same thing, card sections, and flowcharts when something flows. Code never goes inside the HTML. It stays in normal fenced blocks, linked to the render by numbered badges.

![A show-me reply: progress, a flowchart, two cards and a question](assets/demo.png)

*Demo with a made-up project.*

## Install

Works in any agent T3 Code runs. Pick yours:

<details open>
<summary><strong>Claude Code</strong></summary>

```
/plugin marketplace add Dananz/show-me
/plugin install show-me@show-me
```

Run with `/show-me:show-me`. Updates: `/plugin` → Marketplaces → `show-me` → **Enable auto-update**, or `/plugin marketplace update show-me`.

</details>

<details>
<summary><strong>Codex</strong></summary>

```bash
codex plugin marketplace add Dananz/show-me --ref main
codex plugin add show-me@show-me
```

Run with `$show-me`. Updates: `codex plugin marketplace upgrade show-me`, then remove and add the plugin again.

</details>

<details>
<summary><strong>Antigravity (agy)</strong></summary>

```bash
agy plugin install https://github.com/Dananz/show-me
```

Updates: `agy plugin uninstall show-me`, then install again.

</details>

<details>
<summary><strong>Cursor, OpenCode, Pi, and any agent-skills harness</strong></summary>

```bash
npx skills add Dananz/show-me -g
```

Updates: `npx skills update -g`. Without the CLI, copy `skills/show-me` into the folder your agent scans for skills.

</details>

Then ask for something and say `show me`. It stays on for the session. Say `stop visual mode` to turn it off.

## What it does

- **Result first, then one plain sentence** of context, so someone who wasn't following still gets it.
- **Color means one thing:** green done, amber tradeoff, red broken, blue info. Theme variables only, so light and dark both work.
- **Flowcharts** for anything with order or branches (plain cards for linear steps, Mermaid for branches).
- **Asks look like asks:** a decision you need to make gets its own card with the options spelled out.
- **Code stays text:** T3 stacks every render above the reply, so code goes below in fenced blocks, matched to the render by ① ② ③.
- **Hebrew / RTL** support: `dir="rtl"`, flipped arrows, `RL` flowcharts.

Inspired by the [i-have-adhd](https://github.com/ayghri/i-have-adhd) skill for actionable, low-friction answers.

## Files

- `skills/show-me/SKILL.md`: the rules.
- `skills/show-me/template.html`: a starter render with cards, a linear flow, a progress row and a Mermaid chart.
- `plugin.json` (Codex, Antigravity), `.claude-plugin/` (Claude Code), `.agents/plugins/marketplace.json` (Codex marketplace).

## Requirements

T3 Code with the `html_preview` and `html_render` tools. Elsewhere it falls back to markdown.

## License

MIT
