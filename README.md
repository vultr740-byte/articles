# Articles

A tiny GitHub Pages repo for sharing articles (original + translations).

## Before you publish (MANDATORY)

**Read this README first.** If the task involves web fetching, full-text translation, images, or multiple file writes, **use a subagent** and follow the checklist below.

### 1) When to use a subagent

Use a subagent by default for anything that is likely to take >30s or can fail midway:

- Browser automation / web extraction
- Full-text translation
- Multi-file edits (post + index + assets)
- Any workflow that may require retries

### 2) Workflow (fetch → verify → translate → verify → publish)

#### A. Fetch & snapshot

1. Fetch/extract the source article.
2. Save a **verbatim extraction snapshot** under `tmp/` (outside of `posts/`) so you always have a ground-truth reference.
3. Verify the snapshot is complete:
   - Contains the ending line(s)
   - Contains the acknowledgements section (if any)
   - Reasonable file size / line count (no truncation)

#### B. Translate (strict segmented translation)

**Never translate an entire long article in one shot.**

- Translate in **small chunks** (1–2 paragraphs at a time).
- Write chunk outputs to temporary files (e.g. `tmp/zh_parts/part-001.md`) and only assemble the final `zh.md` at the end.
- Avoid repeated overwrite of `zh.md` while translating.

#### C. Assemble final files

- `posts/<slug>/en.md`: English/original content
- `posts/<slug>/zh.md`: Chinese translation
- Use YAML front matter at the top of both:

```yaml
---
title: "..."
source: "https://..."
date: "YYYY-MM-DD"
---
```

#### D. Verification before commit

Run these checks before committing:

1) **Completeness**
- `zh.md` contains the translated equivalent of the ending section (and acknowledgements if present).

2) **No mixed-language body**
- No English paragraphs in `zh.md` (allow: tickers, proper nouns, URLs).

3) **No duplicate titles**
- If the site layout already renders the title from front matter, **do not add an extra `# Title`** in the body.

4) **Images render correctly**
- No raw Substack image URLs as plain text.
- Use Markdown embeds: `![](https://...)`
- No broken nested image syntax like:
  - `![](\n![](url)\n)`

5) **Long headlines don’t overflow**
- Convert long “headline | source” lines into blockquotes with line breaks.

6) **No editorial notes in body**
- Don’t include “fetched at … / extracted via …” in the article body.

### 3) Style glossary (keep consistent)

Preferred translations for key finance/macro terms:

- self-reinforcing loop / feedback loop (in this article’s sense) → **自我强化回路**
- money-good → **钱好资产** (first use: add a short parenthetical explanation)
- interchange (card payments) → **交换手续费/交换费率**
- forward deployed engineers → **前置交付/驻场部署工程师**

### 4) Commit & push

- Commit message should state what changed (e.g. "Add post …", "Fix zh translation terms", "Embed images").
- Push to `main`.
- After pushing, quickly spot-check the live page (cache may take a few minutes).

## Structure

- `index.md`: homepage / directory
- `posts/<slug>/en.md`: original
- `posts/<slug>/zh.md`: Chinese translation

## Publishing

Enable GitHub Pages:

- Repo → Settings → Pages
- Source: **Deploy from a branch**
- Branch: `main` / Folder: `/ (root)`

Then your site will be available at:
`https://vultr740-byte.github.io/articles/`
