# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **static HTML knowledge base** of security-engineering study notes (~900 `.html` files, no other source languages). It is served as a plain website — there is **no build system, no package manager, no test suite, no JS/CSS source files**. Everything (styles, scripts) is inlined into each HTML file or loaded from CDNs. `.nojekyll` marks it for direct GitHub Pages serving.

Because there is no toolchain, "developing" here means **editing HTML directly and opening it in a browser**. There is nothing to compile, lint, or test.

- Preview locally: open a file in a browser, or `python -m http.server` from the repo root and browse to `http://localhost:8000`. A server (not `file://`) is needed for the Mermaid ES-module import and relative navigation to work.

## Repository structure

- `index.html` — the **hand-authored dashboard / entry point**. A searchable card grid of every note, grouped into collapsible topic sections, with client-side search (`doSearch`) and per-card "reviewed" progress tracked in `localStorage`. This is the primary navigation surface. **`indexv2.html` is a byte-identical copy** — if you change one, change both or reconcile them.
- Topic directories (`ai_security/`, `auth_authz/`, `cloud/`, `network/`, `code/`, `crypto/`, `web/`, `os/`, etc.) — each subtopic is its own folder containing a `README.html` (the note). Deeper nesting mirrors the taxonomy, e.g. `auth_authz/Tokens/JWT/README.html`, `cloud/AWS/Compute/Lambda/README.html`.
- `*/README.html` at a section root (e.g. `ai_security/README.html`) — the section's overview/landing note.
- `*/_PROGRESS.html` — per-section note-creation checklists (`[x]`/`[ ]` items) tracking which topics have been written. Update these when adding or completing notes.
- Top-level roll-up notes: `security-engineering-study-notes.html`, `security-engineering-guide.html`, `security-engineering-rapid-review.html`, `two-week-study-roadmap.html`, `README.html`.
- `scenarios/`, `questions/`, `question-bank/`, `practice-questions/` — interview/practice material; `scenarios/1_hour_long/QNN/` folders include `architecture.png` diagrams.

## The page template

Content notes (`README.html` files) are **self-contained** and share one consistent template. When creating or editing a note, preserve this structure:

- **Mermaid diagrams** via `<div class="mermaid">…</div>`, rendered lazily with an `IntersectionObserver` (module import from `cdn.jsdelivr.net`, `startOnLoad:false`, `theme:'neutral'`). Large pages rely on this lazy render to stay scrollable — don't switch to eager `startOnLoad`.
- **Syntax highlighting** via highlight.js from `cdnjs.cloudflare.com` (`hljs.highlightAll()`) with the `github` stylesheet.
- **Inline `<style>`** in `<head>` (GitHub-markdown-like styling; no external CSS).
- **Inline pan/zoom JS** at the end of `<body>` for diagrams (mouse drag + touch pinch-zoom).

All third-party assets come from CDNs — the site has no vendored/bundled dependencies.

## Conventions & known quirks

- Notes were authored in Markdown and converted to HTML. As a result, **many internal cross-links still point to `.md` targets that don't exist** (e.g. `href="security-engineering-study-notes.md#…"`); ~72 files contain such links. Real navigation goes through `index.html` (which links to the correct `.html` paths). When adding links between notes, link to the actual `.html` file, not `.md`.
- Directory and topic names use `Title-Case-With-Hyphens`; note files are almost always named `README.html`.
- `index.html` card entries are static HTML (`<div class="card" data-path="…">…</div>`) — adding a new note means **manually adding its card** to the relevant `<section class="topic-sec">` in both `index.html` and `indexv2.html`, and (optionally) ticking it off in the section's `_PROGRESS.html`.
