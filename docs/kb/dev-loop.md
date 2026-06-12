---
type: reference
title: "dev-loop"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# Dev Loop

## Build

GitHub Pages builds automatically on push to `master`. For a local build to `_site/`:

```bash
bundle exec jekyll build
```

Docker alternative (produces identical output):
```bash
docker compose run --rm jekyll jekyll build
```

## Test

No automated test suite. Verification is manual. Full checklist:

```bash
# 1. Start local server
docker compose up

# 2. Confirm server started
# Expected log line: Server address: http://0.0.0.0:4000

# 3. Smoke test HTTP 200
curl -s -o /dev/null -w "%{http_code}" http://localhost:4000
# Expected: 200
```

Manual checks (all must pass before push):

| Check | Expected behavior |
|---|---|
| Dark theme on first load | Loads in dark mode immediately — no white flash; `localStorage['theme']` persists |
| Theme toggle | Moon/sun icon switches between dark and light; page re-renders correctly |
| Highlights grid | CSS Grid stat-card layout — NOT a bullet list |
| Skill tags | JetBrains Mono monospace font, accent borders, hover inversion |
| Avatar | Circular 800×800 with accent ring |
| No raw Liquid output | No `{{` or `{%` visible in rendered page |
| Print preview | White background, action buttons hidden, cards visible |
| PDF text selectability | Exported PDF text is selectable and copies as plain text (not image-based) |

## Lint / type-check

No lint toolchain for this project (Jekyll + YAML + Liquid). YAML validation:

```bash
ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"
```

Expected output: `OK`. Non-zero exit with a parse error message means `_data/data.yml` is malformed.

## Run locally

```bash
docker compose up
```

Browse to `http://localhost:4000`. Source changes to `_data/data.yml`, `_includes/`, `_layouts/`, and `_sass/` hot-reload via `--livereload`. If edits don't appear, confirm `--force_polling` is still present in `docker-compose.yml` — it is required for Windows bind mounts.

Without Docker:
```bash
bundle install   # first time only
bundle exec jekyll serve --watch --livereload --host 0.0.0.0
```

## Release

No versioned releases. Deployment is continuous:

1. Edit content in `_data/data.yml` (cross-reference `Sean Cullen - Resume.docx` for metrics).
2. Validate YAML locally.
3. Verify rendering manually at `localhost:4000`.
4. Push `master` to GitHub origin.
5. GitHub Pages rebuilds automatically (typically 30–90 seconds).
6. Verify production at `https://career.secop.dev`.

## Open plan

Two tracks of `docs/superpowers/plans/2026-05-18-site-verification-and-activation.md` remain open:

- **Task 2 — Google Search Console activation:** requires the verification token from Sean. Once provided, inject into `_config.yml`, verify meta tag render, push to trigger rebuild, then confirm verification in Search Console.
- **Task 3 — Certification content gap closure:** populate `start`/`end` dates and Credly badge URLs for 13 cert entries in `_data/data.yml` from provider records and Credly profile (`https://www.credly.com`). Validate YAML, verify rendering, commit.

## Preferred agentic loop

1. Read `_data/data.yml` to understand current content structure before making changes.
2. Cross-reference `Sean Cullen - Resume.docx` (repo root) for any metric or achievement bullets.
3. Edit `_data/data.yml` — single atomic change per concept.
4. Run `ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"` to validate.
5. Run `docker compose up` and navigate to `http://localhost:4000` to verify rendering.
6. Check the manual verification table above (all must pass).
7. Push to `master`; verify at `https://career.secop.dev`.
