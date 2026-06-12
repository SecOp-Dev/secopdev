---
type: reference
title: "config"
tags: [type/reference]
created: 2026-06-11
updated: 2026-06-11
---

# Configuration

This project has no environment variables or `.env` file. All configuration is through Jekyll YAML config files and the Docker Compose service definition.

## Environment variables

No runtime environment variables. The project reads no `ENV` vars in templates or build scripts.

## Config files

| File | Format | Purpose |
|---|---|---|
| `_config.yml` | YAML | Jekyll site configuration: URL, title, description, build settings, plugin list, Sass options |
| `_data/data.yml` | YAML | **Primary content source.** All resume sections — experiences, highlights, skills, certifications, projects, recommendations. Edit here, not in templates. |
| `Gemfile` | Ruby DSL | Ruby gem dependencies: Jekyll 4.0, plugins (jekyll-feed, jekyll-sitemap, etc.) |
| `Gemfile.lock` | Ruby lock | Pinned gem versions — commit this; do not delete unless resolving a corrupt state |
| `docker-compose.yml` | YAML | Local dev container: `jekyll/builder:4.0`, volume mount, port 4000, `--force_polling` flag |
| `CNAME` | Plain text | GitHub Pages custom domain — contains `career.secop.dev` |

## `_config.yml` key settings

```yaml
title: "Sean Cullen - Director of Security Operations"
url: "https://career.secop.dev"
description: "Sean Cullen — Director of Security Operations & Cloud Security Architect specializing in SIEM/SOAR detection engineering, threat hunting, DFIR, automated incident response, and Microsoft Sentinel & Azure security."

# Jekyll 4.0
# Sass: compressed output
sass:
  style: compressed

# Build settings
compress_site: true
safe: false
encoding: utf-8
```

## `docker-compose.yml` key settings

```yaml
services:
  jekyll:
    image: jekyll/builder:4.0
    command: jekyll serve --watch --force_polling --verbose --livereload --host 0.0.0.0
    ports:
      - "4000:4000"
    volumes:
      - .:/srv/jekyll
```

**Critical:** `--force_polling` must remain — Windows bind mounts do not deliver inotify events to the container.

## `_data/data.yml` structure

The file drives every section of the resume. Top-level keys:

| Key | Section rendered | Template |
|---|---|---|
| `sidebar` | Profile photo, contact info, interests | `_includes/sidebar.html` |
| `career-profile` | Summary paragraph | `_includes/career-profile.html` |
| `highlights` | Stat-card grid (metrics) | `_includes/highlights.html` |
| `experiences` | Job history | `_includes/experiences.html` |
| `education` | Education history | `_includes/education.html` |
| `certifications` | Certifications & Training | `_includes/certifications.html` |
| `projects` | Key initiatives | `_includes/projects.html` |
| `skills` | Tag-badge grid (categories array) | `_includes/skills.html` |
| `recommendations` | LinkedIn testimonials | `_includes/recommendations.html` |

## Source-of-truth rule

`Sean Cullen - Resume.docx` (repo root) is the **source of truth for quantified metrics**. Cross-reference it before adding or changing any achievement bullets in `_data/data.yml`. Never invent or soften numbers.
