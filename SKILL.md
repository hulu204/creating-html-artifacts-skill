---
name: creating-html-artifacts
description: Use when creating HTML artifacts for a dense answer, design discussion, architecture proposal, implementation plan, report, review, explainer, prototype, deck, or editing task that would exceed roughly 500 words or Chinese characters, or would be easier to understand as a self-contained HTML page with layout, diagrams, interaction, export-to-PDF controls, table-of-contents navigation, original markdown handoff panels, copyable approval text, and best-effort auto-open behavior.
version: 0.1.0
author: hulu204
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  tags: [html, artifacts, reports, plans, design, pdf, visualization, markdown, decision-making, human-in-the-loop]
---

# Creating HTML Artifacts

Create self-contained HTML artifacts when a normal chat answer would become too long, too spatial, or too hard to review inline. The goal is to turn dense prose into a file the user can scan, open, export to PDF, and hand off.

Core principle: if the user must compare options, hold a design in their head, review a plan, understand a process, or revisit a report later, make an HTML artifact instead of a wall of markdown.

## When to Use

Use this skill when any of these are true:

- The answer is a design discussion, architecture proposal, implementation plan, technical review, status report, research brief, or decision memo that would likely exceed roughly 500 words or 500 Chinese characters.
- Side-by-side layout, visual hierarchy, diagrams, cards, timelines, tables, filters, tabs, or collapsible sections would reduce cognitive load.
- The user asks for an artifact, HTML page, visual plan, report, dashboard, prototype, deck, PDF handoff, or something they can open in a browser.
- A short answer would hide important trade-offs, evidence, assumptions, risks, or next steps.

Prefer plain markdown when the answer is short, linear, terminal-native, or the user explicitly asks for text only.

## Skill-Level Long-Answer Trigger

Before writing a long response, estimate whether the final answer would exceed roughly 500 words or 500 Chinese characters.

If yes, and the content is a design, planning, review, report, explainer, comparison, or decision task, strongly prefer an HTML artifact over a long inline answer when this skill is loaded:

1. Write a real self-contained `.html` file.
2. Include the default top-right `Export PDF` control and print CSS.
3. Include table-of-contents navigation plus the required handoff sections: original markdown source and suggested approval phrase, each with copy buttons.
4. Validate the file with the checks below.
5. Open the file automatically when possible.
6. Reply with only a short summary, the absolute path, and 3-5 inspection hints.

This is a skill-level trigger, not a runtime hook. It is stable when the skill is loaded, but public skill installation alone cannot force every future long answer to load this skill. Do not claim global automatic enforcement. If hard enforcement is required, use a separate opt-in runtime hook or auto-trigger mechanism. If the user explicitly requests plain text, obey the user.

## Workflow

1. Name the artifact by outcome, not format: `implementation-plan.html`, `incident-timeline.html`, `pr-review.html`, `architecture-options.html`.
2. Choose a safe output path:
   - Inside a project: `artifacts/<artifact-name>.html` or `.artifacts/<artifact-name>.html`.
   - Outside a project: `/tmp/html-artifacts/<artifact-name>.html`.
   - Avoid overwriting existing artifacts unless the user asked to update one.
3. Gather only the source material needed. If evidence is missing, state assumptions and unknowns inside the page.
4. Pick an artifact pattern from the catalog below.
5. Write one standalone HTML file:
   - Start with `<!doctype html>`.
   - Include `<meta name="viewport" content="width=device-width, initial-scale=1">`.
   - Inline CSS and JavaScript.
   - No CDN, remote fonts, remote scripts, tracking, or build step.
   - Responsive layout for laptop and phone.
6. Add the default PDF export control from the next section.
7. Add navigation: title, context, TL;DR, and a table of contents or sticky sidebar.
8. Add the required design handoff sections: `Original Markdown Plan` and `Suggested Approval Phrase`, with copy buttons.
9. Add interaction only when it changes understanding: tabs, filters, collapses, sliders, hover notes, copy/export buttons.
10. Run the static validation check. If browser tools are available, also check the console for JavaScript errors.
11. Open the artifact automatically when possible.
12. Final response: keep it short; include the absolute path and what to inspect.

## Default PDF Export Control

Every generated artifact must include a stable top-right PDF export button. Use browser print because it is dependency-free, offline, and cross-platform. The button opens the system print dialog; the user can choose "Save as PDF".

Place this near the start of `<body>`:

```html
<button class="print-button no-print" onclick="window.print()" aria-label="Export PDF">
  Export PDF
</button>
```

Add this CSS to every artifact, adapting colors only if needed:

```css
.print-button {
  position: fixed;
  top: 18px;
  right: 18px;
  z-index: 9999;
  border: 1px solid #d8c7b8;
  background: #fffdf8;
  color: #9a4f32;
  padding: 10px 14px;
  border-radius: 999px;
  font-weight: 700;
  cursor: pointer;
  box-shadow: 0 6px 18px rgba(0,0,0,.08);
}
.print-button:hover { background: #fff3e8; }
.print-button:focus-visible { outline: 3px solid #f2b880; outline-offset: 2px; }

@page { size: A4; margin: 14mm; }

@media print {
  body { background: #fff !important; }
  .print-button,
  .no-print,
  nav,
  .sticky,
  .floating-controls {
    display: none !important;
  }
  .wrap,
  .page,
  main {
    max-width: none !important;
    margin: 0 !important;
    box-shadow: none !important;
    border: 0 !important;
  }
  a { color: inherit; text-decoration: none; }
  section,
  article,
  table,
  figure,
  pre,
  blockquote {
    break-inside: avoid;
    page-break-inside: avoid;
  }
}
```

Do not promise a silent one-click PDF download from a local HTML file. Browsers intentionally require the print dialog for saving PDF without extra dependencies.


## Required Handoff Sections

For design proposals, implementation plans, architecture comparisons, and review artifacts, include two handoff sections near the bottom of the page:

1. `Original Markdown Plan / 原始方案全文` — a collapsed `<details>` panel containing the full original markdown plan that the HTML artifact was derived from.
2. `Suggested Approval Phrase / 建议批准语` — a short review/approval phrase the user can copy and paste to approve the plan, request execution, or send to a collaborator.

Both sections must have copy buttons. For Chinese artifacts, label the user-facing controls as `目录`, `原始方案全文`, `展开查看原始 markdown 方案`, `建议批准语`, `复制批准语`, and `复制 markdown 原文` where appropriate:

```html
<section id="handoff" class="handoff" aria-labelledby="handoff-title">
  <h2 id="handoff-title">Handoff</h2>
  <div class="handoff-grid">
    <article class="card" id="approval-phrase">
      <div class="section-heading">
        <h3>Suggested Approval Phrase / 建议批准语</h3>
        <button class="copy-button no-print" data-copy-target="approvalText">Copy approval phrase / 复制批准语</button>
      </div>
      <p id="approvalText">Approved. Please proceed with the recommended plan, keeping the stated scope, validation checkpoints, and rollback criteria.</p>
    </article>
    <article class="card" id="original-markdown">
      <div class="section-heading">
        <h3>Original Markdown Plan / 原始方案全文</h3>
        <button class="copy-button no-print" data-copy-target="originalMarkdown">Copy markdown source / 复制 markdown 原文</button>
      </div>
      <details>
        <summary>Expand to view the original markdown plan / 展开查看原始 markdown 方案</summary>
        <pre id="originalMarkdown"><code>## Original plan

Paste the complete source markdown here, escaped as text.</code></pre>
      </details>
    </article>
  </div>
</section>
```

Add a small copy helper:

```html
<script>
document.querySelectorAll('[data-copy-target]').forEach((button) => {
  button.addEventListener('click', async () => {
    const target = document.getElementById(button.dataset.copyTarget);
    const text = target ? target.innerText : '';
    try {
      await navigator.clipboard.writeText(text);
      const original = button.textContent;
      button.textContent = 'Copied / 已复制';
      setTimeout(() => { button.textContent = original; }, 1200);
    } catch (error) {
      button.textContent = 'Copy failed / 复制失败';
    }
  });
});
</script>
```

If there is no separate original markdown because the artifact was composed directly, create a concise markdown source version that preserves the final plan, recommendation, assumptions, and validation checklist. Do not leave placeholder text in delivered artifacts.

## Auto-open After Generation

After the artifact passes static validation, open it automatically when the runtime appears local and a terminal tool is available.

Use a best-effort platform opener:

```bash
ARTIFACT_PATH="/absolute/path/to/artifact.html"
python3 - "$ARTIFACT_PATH" <<'PY'
import os, platform, shutil, subprocess, sys
try:
    path = os.path.abspath(sys.argv[1])
    system = platform.system().lower()
    if system == 'darwin':
        subprocess.run(['open', path], check=False)
    elif system == 'windows':
        os.startfile(path)  # type: ignore[attr-defined]
    else:
        opener = shutil.which('xdg-open') or shutil.which('sensible-browser')
        if opener:
            subprocess.run([opener, path], stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL, check=False)
        else:
            print('auto-open skipped: no opener found')
except Exception as exc:
    print(f'auto-open skipped: {exc}')
PY
```

If auto-open fails because the session is remote, headless, containerized, or lacks GUI tools, do not treat it as task failure. Report the absolute path so the user can open it manually.

## Artifact Pattern Catalog

| Need | HTML pattern | Include |
| --- | --- | --- |
| Choose among options | Side-by-side comparison cards | trade-offs, risks, recommendation, decision criteria |
| Hand off implementation | Implementation plan page | milestones, data flow, risky code, validation plan, risk table |
| Review code | Annotated PR / diff page | file tour, severity labels, jump links, focus areas |
| Understand a codebase | Module map | boxes/arrows, entry points, hot path, ownership notes |
| Explain process | Annotated flowchart or timeline | steps, timings, failure paths, inputs/outputs |
| Present update | Arrow-key slide deck | concise slides, speaker-friendly structure, one decision ask |
| Teach concept | Interactive explainer | TL;DR, live demo, glossary, comparison table |
| Report status | Status dashboard | metrics, highlights, slips, risks, next actions |
| Explain incident | Incident timeline | TL;DR, events, root cause, follow-ups |
| Let user edit state | Custom editor | controls, validation warnings, copy/export output |

## Default Visual Direction

Default to a warm editorial document unless the user asks for another style:

- Light warm-gray background with a centered near-white paper canvas.
- Large serif hero headline (`Georgia`, `Noto Serif SC`, serif fallback), with one key phrase highlighted by a hand-drawn yellow marker effect.
- Sans-serif body copy with generous line-height, restrained text width, and whitespace.
- Thin warm-gray borders, soft shadows, rounded but not bubbly cards.
- Orange-red accent for primary actions; cyan, green, and yellow only as small supporting signals.
- Airy tables: light beige header, fine dividers, right-aligned numbers, subtle row status tint.
- Simple inline SVG/CSS charts; avoid heavy gradients, dark dashboards, glassmorphism, and saturated enterprise SaaS styling.
- First viewport should feel like a polished report cover, not a terminal dashboard.

## Implementation Rules

- Make the first viewport useful: title, context, scope, status, and the key conclusion.
- Favor semantic HTML (`main`, `section`, `article`, `nav`, `table`, `details`) over div soup.
- Use CSS variables for palette and spacing.
- Use accessible contrast, visible focus states, and labels for controls.
- Keep JavaScript small and local. Interactions should be understandable from the file itself.
- Put source data in one `const data = ...` block when the page is data-heavy.
- Add `Copy summary`, `Copy markdown`, `Copy JSON`, `Copy diff`, or `Download SVG` buttons when the user will feed the result into another workflow.
- For custom editors, always provide a paste-ready export. Never make the UI a dead end.
- Include evidence: files read, metrics, logs, assumptions, source dates, or prompt summary when relevant.

## Minimal Starter Skeleton

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Artifact title</title>
<style>
:root { --bg:#faf9f5; --paper:#fffdf8; --text:#171512; --muted:#6f675f; --accent:#c75f3a; --line:#e4d8ca; }
* { box-sizing: border-box; }
body { margin:0; background:var(--bg); color:var(--text); font:15px/1.6 system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif; }
.wrap { max-width:1120px; margin:0 auto; padding:48px 28px; }
.hero { background:var(--paper); border:1px solid var(--line); border-radius:22px; padding:34px; box-shadow:0 18px 60px rgba(70,50,20,.08); }
h1 { font-family: Georgia, "Noto Serif SC", serif; font-size:clamp(34px,6vw,68px); line-height:1.02; margin:0 0 14px; }
.grid, .handoff-grid { display:grid; grid-template-columns:repeat(auto-fit,minmax(260px,1fr)); gap:16px; margin-top:18px; }
.card { background:#fff; border:1px solid var(--line); border-radius:16px; padding:18px; }
.toc { margin:18px 0; padding:14px 18px; background:#fff; border:1px solid var(--line); border-radius:14px; }
.section-heading { display:flex; align-items:center; justify-content:space-between; gap:12px; }
.copy-button { border:1px solid var(--line); background:#fff8ef; color:#9a4f32; border-radius:999px; padding:8px 12px; font-weight:700; cursor:pointer; }
pre { white-space:pre-wrap; overflow:auto; background:#28231f; color:#fffaf1; border-radius:14px; padding:16px; }
.badge { color:var(--accent); font-weight:800; letter-spacing:.04em; text-transform:uppercase; }
.print-button { position:fixed; top:18px; right:18px; z-index:9999; border:1px solid #d8c7b8; background:#fffdf8; color:#9a4f32; padding:10px 14px; border-radius:999px; font-weight:700; cursor:pointer; box-shadow:0 6px 18px rgba(0,0,0,.08); }
.print-button:hover { background:#fff3e8; }
.print-button:focus-visible { outline:3px solid #f2b880; outline-offset:2px; }
@page { size:A4; margin:14mm; }
@media print { body{background:#fff!important;} .print-button,.no-print,nav,.sticky,.floating-controls{display:none!important;} .wrap,.page,main{max-width:none!important;margin:0!important;box-shadow:none!important;border:0!important;} a{color:inherit;text-decoration:none;} section,article,table,figure,pre,blockquote{break-inside:avoid;page-break-inside:avoid;} }
@media (max-width:640px) { .wrap { padding:28px 16px; } .hero { padding:24px; } }
</style>
</head>
<body>
<button class="print-button no-print" onclick="window.print()" aria-label="Export PDF">Export PDF</button>
<main class="wrap">
  <nav class="toc no-print" aria-label="Table of contents">
    <strong>Contents / 目录</strong>
    <a href="#summary">Summary</a> · <a href="#options">Options</a> · <a href="#handoff">Handoff</a>
  </nav>
  <header class="hero" id="summary">
    <p class="badge">Context / artifact type</p>
    <h1>Artifact title</h1>
    <p>One-sentence purpose, scope, and key result.</p>
  </header>
  <section class="grid" id="options" aria-label="Primary comparison">
    <article class="card"><h2>Option A</h2><p>Key idea.</p></article>
    <article class="card"><h2>Option B</h2><p>Key idea.</p></article>
  </section>
  <section id="handoff" class="handoff" aria-labelledby="handoff-title">
    <h2 id="handoff-title">Handoff</h2>
    <div class="handoff-grid">
      <article class="card" id="approval-phrase">
        <div class="section-heading"><h3>Suggested Approval Phrase / 建议批准语</h3><button class="copy-button no-print" data-copy-target="approvalText">Copy approval phrase / 复制批准语</button></div>
        <p id="approvalText">Approved. Please proceed with the recommended plan, keeping the stated scope, validation checkpoints, and rollback criteria.</p>
      </article>
      <article class="card" id="original-markdown">
        <div class="section-heading"><h3>Original Markdown Plan / 原始方案全文</h3><button class="copy-button no-print" data-copy-target="originalMarkdown">Copy markdown source / 复制 markdown 原文</button></div>
        <details><summary>Expand to view the original markdown plan / 展开查看原始 markdown 方案</summary><pre id="originalMarkdown"><code>## Original plan\n\nPaste the complete source markdown here, escaped as text.</code></pre></details>
      </article>
    </div>
  </section>
</main>
<script>
document.querySelectorAll('[data-copy-target]').forEach((button) => {
  button.addEventListener('click', async () => {
    const target = document.getElementById(button.dataset.copyTarget);
    const text = target ? target.innerText : '';
    try {
      await navigator.clipboard.writeText(text);
      const original = button.textContent;
      button.textContent = 'Copied / 已复制';
      setTimeout(() => { button.textContent = original; }, 1200);
    } catch (error) {
      button.textContent = 'Copy failed / 复制失败';
    }
  });
});
</script>
</body>
</html>
```

## Verification Checklist

Before delivering:

- [ ] File exists at the reported absolute path.
- [ ] File starts with `<!doctype html>` and contains `<meta name="viewport"`.
- [ ] File contains a visible `Export PDF` button that calls `window.print()`.
- [ ] File contains `@media print` CSS that hides `.print-button` / `.no-print`.
- [ ] File contains table-of-contents navigation with links to main sections.
- [ ] Design/review/plan artifacts include `Original Markdown Plan / 原始方案全文` and `Suggested Approval Phrase / 建议批准语` sections.
- [ ] The approval phrase and original markdown source each have working copy buttons (`复制批准语`, `复制 markdown 原文` when the artifact is Chinese).
- [ ] File opens without network access; no CDN, remote fonts, remote scripts, or tracking.
- [ ] First viewport has title, purpose, scope, and key result.
- [ ] Layout works at narrow width and desktop width.
- [ ] Any controls work and have visible labels.
- [ ] Export/copy output is correct when included.
- [ ] Auto-open was attempted when local GUI access appeared available; failure was treated as non-blocking.
- [ ] Final response is short and tells the user where the file is and what to inspect.

Minimum static check when browser tools are unavailable:

```bash
python3 - <<'PY'
from pathlib import Path
p = Path('ARTIFACT.html')
s = p.read_text(encoding='utf-8')
low = s.lower()
assert p.exists() and p.stat().st_size > 500
assert '<!doctype html' in low[:100]
assert '<meta name="viewport"' in low
assert '<style' in low
assert 'window.print()' in s
assert '.print-button' in s
assert '@media print' in s
assert 'Original Markdown Plan' in s
assert 'Suggested Approval Phrase' in s
assert 'data-copy-target' in s
assert 'Table of contents' in s or 'Contents' in s or '目录' in s
assert 'http://' not in s and 'https://' not in s
print('html artifact static check OK:', p.resolve())
PY
```

## Common Pitfalls

1. Recreating markdown inside HTML. If the page is still a linear essay, the artifact failed to use the medium.
2. Forgetting PDF export. Every artifact should include the print button and print CSS unless the user explicitly forbids controls.
3. Forgetting the handoff sections. Design and plan artifacts need original markdown source plus a suggested approval phrase, both copyable; Chinese artifacts should expose them as `原始方案全文`, `建议批准语`, `复制批准语`, and `复制 markdown 原文`.
4. Claiming silent PDF download. Use `window.print()` and explain Save as PDF if asked.
5. Treating auto-open failure as fatal. Headless or remote environments are normal; report the path.
6. Overbuilding a product. These artifacts are disposable and self-contained. Avoid frameworks, auth, persistence, and package installs unless explicitly requested.
7. Pretty but ungrounded. Include evidence, source files, metrics, assumptions, or the prompt that shaped the artifact.
8. Interaction without purpose. Sliders, tabs, and animations are only valuable when they help the user decide, understand, or validate.
9. Unreadable on mobile or PDF. Wide tables, diagrams, and grids need wrapping, scaling, or alternate summaries.
