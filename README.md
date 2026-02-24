# Articles

A minimal GitHub Pages repo for publishing bilingual posts (EN + 中文) with a predictable structure and a repeatable workflow.

> **Rule 0 (MANDATORY):** Before starting any new post, read this README top-to-bottom.

---

## 1) What this repo is

- Purpose: publish shareable, fast-loading posts.
- Inputs: a URL (blog/substack/docs/tweet thread, etc.) and optionally your own notes.
- Outputs:
  - An English/original post (`en.md`)
  - A Chinese translation (`zh.md`)
  - Index updated so the post appears on the homepage

---

## 2) Homepage (index)

The homepage is powered by `index.html` front matter.

- The list of posts is maintained in `index.html` under `items:`.
- Each item can have:
  - `date: "YYYY-MM-DD"`
  - `title: "..."`
  - `zh: "/posts/<slug>/zh"` (required)
  - `en: "/posts/<slug>/en"` (optional)

Example:

```yaml
---
title: "Articles"
items:
  - date: "2026-02-23"
    title: "THE 2028 GLOBAL INTELLIGENCE CRISIS"
    en: "/posts/2026-02-23-2028gic/en"
    zh: "/posts/2026-02-23-2028gic/zh"
---
```

**Homepage rules**
- New posts go to the top (most recent first).
- `zh` is the default click target for the title.
- **Homepage language rule:** Homepage titles are Chinese.
- **Homepage link rule:** Keep the homepage list consistent. By default, **do not show per-post language toggles on the homepage** (no `en:` field in `index.html`). Users can switch languages from the post landing page.
- Keep titles short to avoid layout overflow.

---

## 3) Repository layout

```
.
├─ index.md                     # Homepage directory (list of posts)
├─ posts/
│  └─ YYYY-MM-DD-<slug>/        # One post directory per article
│     ├─ en.md                  # English/original
│     ├─ zh.md                  # Chinese translation
│     └─ index.html             # Post landing page (links to en/zh)
├─ assets/                      # Site CSS
└─ tmp/                         # Workspace scratch (never publish)
```

**Slug rules**
- Use lowercase, hyphen-separated.
- Prefer something short and stable (e.g. `2026-02-23-2028gic`).

---

## 3) Standard front matter

Both `en.md` and `zh.md` MUST start with:

```yaml
---
title: "<title>"
source: "https://..."
date: "YYYY-MM-DD"
---
```

**Important**
- Do **not** add a duplicate H1 (`# Title`) in the body if the site already renders the title from front matter.
- `date` is the publish date for this repo (not necessarily the article’s original publication date).

---

## 4) Publishing workflow (fetch → verify → translate → verify → publish)

### 4.1 Use a subagent (default)

Use a subagent for anything likely to take >30s or fail midway:

- Web extraction / browser automation
- Full-text translation
- Multi-file edits (post + index + assets)
- Any process needing retries

Main session should only:
- confirm output format
- review results
- approve final publish

### 4.2 Fetch & snapshot (MUST)

1) Fetch/extract the source text.
2) Save a verbatim extraction snapshot under `tmp/` (never under `posts/`).
   - Example: `tmp/sources/<slug>.md`
3) Verify snapshot completeness:
- Contains an ending section (not cut off)
- Contains acknowledgements/footnotes if present
- Reasonable file size/line count

### 4.3 Translate (STRICT segmented translation)

**Never translate a long article in one shot.**

- Translate in small chunks (1–2 paragraphs).
- Write chunk outputs to `tmp/zh_parts/<slug>/part-001.md` etc.
- Assemble `posts/<slug>/zh.md` only after all chunks exist.

### 4.4 Assemble post files

- `posts/<slug>/en.md` — original/English
- `posts/<slug>/zh.md` — Chinese translation
- `posts/<slug>/index.html` — landing page
- `index.md` — add entry

### 4.5 Verification checklist (MUST do before commit)

#### A) Completeness
- `zh.md` includes the translated ending (and acknowledgements if any)

#### B) No mixed-language body
- No English paragraphs in `zh.md` (allowed: proper nouns, tickers, URLs)

#### C) Headline formatting
- Convert long “headline | source” lines into **blockquote** with line breaks

#### D) Images
- No raw Substack image URLs as plain text
- Use Markdown embeds: `![](https://...)`
- No broken nested image syntax (e.g. `![](\n![](url)\n)`)
- Global CSS should keep images within container width

#### E) No editorial/process notes in body
- Do not include: “fetched at … / extracted via … / tool name …”

#### F) Source placement
- Keep `source:` in front matter.
- At the end of the post body, add:

```md
---

来源：https://...
```

---

## 5) Links policy (IMPORTANT)

When presenting source links (e.g. from Google/web search, Grok, or user-provided URLs):

- **Do not rewrite, normalize, or "clean" links.**
- **Output exactly what the source returned** (verbatim copy/paste).
- Allowed: formatting only (line breaks / bullet points) without changing the URL.

Rationale: link rewriting is a common source of accidental breakage (e.g. incorrectly prefixing with `x.com`).

---

## 6) Translation glossary (keep consistent)

Preferred translations for common finance/macro terms:

- self-reinforcing loop / feedback loop (in this article’s sense) → **自我强化回路**
- money-good → **钱好资产**（首次出现可括注：接近现金、极低违约风险）
- interchange (card payments) → **交换手续费/交换费率**
- forward deployed engineers → **前置交付/驻场部署工程师**

---

## 6) Commit & push

- Commit messages should describe the change:
  - `Add post: <slug>`
  - `Fix zh translation terms`
  - `Embed images`
  - `Constrain images width`
- Push to `main`.
- After push, spot-check the live page (cache may take 1–5 minutes).

Site URL:
- https://vultr740-byte.github.io/articles/

---

## 7) GitHub Pages setup

- Repo → Settings → Pages
- Source: Deploy from a branch
- Branch: `main` / Folder: `/ (root)`
