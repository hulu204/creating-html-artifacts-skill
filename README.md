# creating-html-artifacts skill

A general-purpose agent skill for turning dense Markdown plans into reviewable HTML artifacts during design, review, comparison, and decision-making.

Markdown remains the source of truth; HTML becomes the review interface. The goal is not HTML vs. Markdown, but reducing human-in-the-loop friction so humans and AI can reach better decisions together.

## What it adds

- Skill-level trigger for long design/planning/review/report answers.
- Self-contained HTML output with no CDN or build step.
- Stable top-right `Export PDF` button using `window.print()`.
- A4 print CSS.
- Table-of-contents navigation.
- Handoff sections for `Original Markdown Plan / 原始方案全文` and `Suggested Approval Phrase / 建议批准语`.
- Copy buttons for approval text and original markdown.
- Best-effort auto-open after local generation.

## Use

Copy `SKILL.md` into any agent or skill system that supports markdown-based skills/instructions, or reference the raw file directly from your agent configuration.

If your agent supports command-line skill installation, install from the raw `SKILL.md` URL:

```bash
# Example only; replace with your agent's skill-install command if it has one.
<agent> skills install https://raw.githubusercontent.com/hulu204/creating-html-artifacts-skill/main/SKILL.md
```

## Boundary

This is a skill-level workflow. It is stable when the skill is loaded, but installing a public skill alone does not force an agent runtime to auto-load it for every future long answer. Hard global enforcement requires an opt-in runtime hook or auto-trigger mechanism.
