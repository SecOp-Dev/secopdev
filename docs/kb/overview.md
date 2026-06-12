---
type: reference
title: "overview"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# Overview

## Purpose

Portfolio website and online resume for Sean Cullen, a cloud security engineer/architect targeting Director/Lead Security Operations, Detection Engineering, and Cloud Security Architect roles at enterprise organizations. The site is hosted at **career.secop.dev** and serves as a metrics-forward, ATS-parseable representation of Sean's professional profile, with dark/light theming and browser-native PDF export.

## Why it exists

A static Jekyll site on GitHub Pages provides zero-infrastructure hosting, full git history for resume versioning, and complete control over layout, styling, and content structure — without the constraints of LinkedIn or a hosted resume builder. Jekyll's Liquid templates and a single YAML data source (`_data/data.yml`) make content updates surgical: edit YAML, push, GitHub Pages rebuilds automatically. The browser print-to-PDF path (rather than html2canvas) ensures the exported PDF is text-selectable and ATS-parseable, which is a hard requirement for the target job market.

## Architecture summary

- **Content layer:** One authoritative YAML file (`_data/data.yml`) drives all resume sections. The source-of-truth for quantified metrics is `Sean Cullen - Resume.docx` in the repo root — cross-reference before editing YAML.
- **Template layer:** Liquid partials in `_includes/` render each section; `_layouts/default.html` assembles the full page with a sidebar; `_layouts/print.html` is the stripped, print-safe variant.
- **Style layer:** Sass/SCSS with CSS custom properties for dark/light theming. Design tokens in `_sass/_tokens.scss`; component styles in `_sass/_base.scss`. The old `_sass/skins/` mechanism is removed.
- **Deployment:** GitHub Pages auto-builds on push to `master`. No CI pipeline — GitHub handles the Jekyll build.
- **Local dev:** Docker Compose wraps `jekyll/builder:4.0` with `--force_polling` and `--livereload` (required on Windows bind mounts where inotify does not fire across the host/container boundary).
- See [architecture.md](architecture.md) for full module and data-flow detail.

## Key constraints

- **No fictitious credentials.** Certifications listed are exam prep/training unless explicitly confirmed as passed.
- **ATS signal is non-negotiable.** Use exact industry terms: MITRE ATT&CK, SIEM, SOAR, DFIR, KQL, Zero Trust.
- **PDF must be text-based.** Do not rewire the PDF button to html2canvas/html2pdf — the browser print path is intentional.
- **Skills section uses the `categories` array**, not the old `toolset` progress-bar structure. Do not revert.
- **Dark mode is the default.** The theme toggle and `_sass/_tokens.scss` token system must be preserved.
- **`--force_polling` must stay in `docker-compose.yml`** — inotify does not work across Windows→container volume mounts.
