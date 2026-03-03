# Articles publishing rules (one-shot, no surprises)

This file is a **short, strict add-on** to the repo’s canonical workflow in `README.md`.

## 0) Source of truth
- **Before starting any new post, read `README.md` top-to-bottom.** (Rule 0)
- This document exists to capture sharp edges we’ve hit in practice.

## 1) Homepage / index policy (ZH-only)
- The homepage list is `index.html` front matter: `items:`.
- **Do not expose EN on the homepage**:
  - `index.html` items must include only: `date`, `title`, `zh`
  - do **not** add `en:`
  - the template must not render any EN buttons

## 2) Markdown separators
### Front matter
- The two `---` lines that wrap YAML front matter are **required**.

### Body
- Do **NOT** use `---` to separate paragraphs.
- Paragraph separation = **blank line**.
- If you need “beat/pause/slide break”, use:
  - extra blank lines, or
  - `&nbsp;`, or
  - `<br><br>`

### TL;DR double-line trap
- The TL;DR `<div class="tldr">…</div>` is styled with a border in the theme.
- Therefore: **never place a body `---` immediately after TL;DR**, otherwise it renders as two stacked lines.

## 3) Preflight checklist (before commit/push)
1) In the post markdown:
- no body `---` used as paragraph breaks
- no body `---` immediately under TL;DR

2) In `index.html`:
- new post is added to the top
- no `en:` fields exist

## 4) Automation (recommended)
Add a preflight script to fail fast on:
- `en:` present in `index.html`
- body `---` immediately after TL;DR
- consecutive body `---`
