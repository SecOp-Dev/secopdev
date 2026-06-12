---
type: reference
title: "architecture"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# Architecture

## Modules

| Module | Purpose |
|---|---|
| `_data/data.yml` | Single authoritative YAML content source — all resume sections live here |
| `_config.yml` | Jekyll site metadata, build options, Sass config, plugin declarations |
| `_layouts/default.html` | Main page layout: sidebar + main content column + action buttons (theme toggle, PDF button) |
| `_layouts/print.html` | Print/PDF layout — strips sidebar, action buttons, and all non-content chrome; white background |
| `_layouts/compress.html` | HTML minification wrapper (wraps `default.html` output) |
| `_includes/head.html` | `<head>` tag: meta, favicon, CSS, no-flash inline theme setter (reads `localStorage`) |
| `_includes/sidebar.html` | Profile photo (circular 800×800 with accent ring), contact info, interests |
| `_includes/career-profile.html` | Executive summary paragraph |
| `_includes/highlights.html` | CSS Grid stat-card grid — quantified impact metrics |
| `_includes/experiences.html` | Job history with company context lines |
| `_includes/certifications.html` | Certifications & Training section with Credly badge links |
| `_includes/projects.html` | Key initiatives cards |
| `_includes/skills.html` | Monospace tag badges grouped by category; branches on `skills.categories` key |
| `_includes/recommendations.html` | LinkedIn testimonials as blockquotes |
| `_includes/scripts.html` | Theme toggle handler + PDF button handler (delegates to `window.print()`) |
| `_includes/footer.html` | Footer |
| `_includes/oss-contributions.html` | Commented out — enable when content exists |
| `_includes/publications.html` | Commented out — enable when content exists |
| `assets/js/pdf-generator.js` | PDF export: delegates to `window.print()` (text-selectable, ATS-parseable) |
| `assets/images/` | Static images; `profile.png` is 800×800 RGB |
| `assets/plugins/bootstrap/` | Bootstrap v3 CSS + JS |
| `assets/plugins/font-awesome/` | Font Awesome v6+ icon library |
| `_sass/_tokens.scss` | Design tokens: color palette, fonts, spacing; dark/light CSS custom properties |
| `_sass/_utilities.scss` | Utility classes |
| `_sass/_base.scss` | Component styles: highlights-list CSS Grid, skill-tag JetBrains Mono badges, card accent rails |

## Data flow

```
Sean Cullen - Resume.docx (source of truth for metrics)
        │
        ▼ (manual transfer)
_data/data.yml ──────────────────────────────────────────┐
                                                          │
_config.yml ──────────────────────────────────────────── │
        │                                                 │
        ▼                                                 ▼
jekyll build ──► Liquid template engine ──► _includes/*.html
                         │                       │
              _layouts/default.html ◄────────────┘
                         │
              _layouts/compress.html (minification)
                         │
                         ▼
                      _site/  (static HTML/CSS/JS)
                         │
            ┌────────────┴────────────┐
            ▼                         ▼
   GitHub Pages (prod)         localhost:4000 (dev)
   career.secop.dev            docker compose up
```

Theme switching path:
```
localStorage['theme'] ──► head.html inline script (no-flash setter)
User clicks toggle ──► scripts.html handler ──► CSS custom properties (_tokens.scss) ──► page re-renders
```

PDF export path:
```
User clicks PDF button ──► scripts.html handler ──► window.print() ──► _layouts/print.html ──► browser print dialog
```

## Dependencies

| Dependency | Version | Purpose |
|---|---|---|
| Jekyll | 4.0 | Static site generator |
| jekyll/builder Docker image | 4.0 | Local dev container (includes Ruby + Bundler + Node) |
| Bootstrap | v3 | Grid, base reset, button styles |
| Font Awesome | v6+ | Icons throughout the UI |
| JetBrains Mono | (web font) | Monospace font for skill tag badges |
| GitHub Pages | — | Hosting + auto-build on push to `master` |
| Sass/SCSS | (bundled with Jekyll 4) | CSS preprocessing and compression |

## Design decisions

No formal ADRs are recorded in this project. The following choices are functionally settled:

- **Browser print-to-PDF over html2canvas/html2pdf** — ensures text selectability and ATS parseability. Do not revert.
- **CSS custom properties token system over `_sass/skins/`** — the `skins/` directory is removed. Do not reintroduce it.
- **Single YAML data source (`_data/data.yml`) over per-section data files** — reduces cognitive overhead for content edits; all content is in one place.
- **`--force_polling` in Docker Compose** — required for Windows bind mounts; inotify events do not cross the host/container mount boundary.
- **GitHub Pages auto-build** — no CI pipeline needed; pushing `master` triggers the build.
