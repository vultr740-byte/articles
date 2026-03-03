# Articles publishing rules (one-shot, no surprises)

## 0) Ground truth
- This repo’s home page list is **`index.html`**.
- The list is manually maintained in the YAML front matter: `page.items`.
- **ZH-only policy (current):** the list should not expose any `en` links.

## 1) Markdown separators
### Front matter
- Every post file must start with YAML front matter.
- The two `---` lines that wrap front matter are **required**.

### Body
- Do **NOT** use `---` to separate paragraphs.
- Paragraph separation = **blank line**.
- If you need “beat/pause/slide break”, use one of:
  - an extra blank line
  - `&nbsp;`
  - `<br><br>`
- Only use `---` in the body when you intentionally want a visible `<hr>`.

### TL;DR block (double-line trap)
- The TL;DR `<div class="tldr">…</div>` is styled with a border in the theme.
- Therefore: **never place a body `---` immediately after TL;DR**, otherwise it renders as “two lines in a row”.

## 2) Index (list) rules
- `index.html` items should include only:
  - `date`, `title`, `zh`
- Do **not** add `en:` fields.
- The template should not render any EN buttons.

## 3) Preflight checklist (before commit/push)
1. Open the rendered page and scan:
   - no “double horizontal lines” between paragraphs
   - TL;DR area looks clean (no extra `<hr>` right under it)
2. In the post markdown, confirm:
   - only the front matter uses `---` near the top
   - any remaining body `---` are intentional
3. In `index.html`, confirm:
   - the new post entry exists
   - no `en:` field is present

## 4) Optional automation (recommended)
Add a small script to lint posts for:
- body `---` directly after TL;DR
- consecutive `---`
- presence of `en:` in `index.html`

(If you want, I can add `scripts/preflight.sh` and wire it into a `make preflight`.)
