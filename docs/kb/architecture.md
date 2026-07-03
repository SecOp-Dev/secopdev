# secopdev-resume — Architecture

## What is this

Jekyll-based online resume website for Sean Cullen, a cloud security engineer and architect focused on detection engineering, cloud security architecture, and incident response. The site is a statically generated, content-driven resume rendered from a single YAML data file and served via GitHub Pages at career.secop.dev. It features a dark-mode-default theme, print/PDF export, and a modular Liquid template system.

## Module breakdown

| Name | Purpose |
|---|---|
| Content layer: `_data/data.yml` | YAML configuration holding all resume content (career profile, education, experiences, skills, certifications, highlights) |
| Template layer: `_includes/*` | 18 Liquid partials (sidebar, career-profile, experiences, skills, certifications, etc.) rendering sections from YAML |
| Styling: `_sass/` | SCSS with token-based theme system (dark mode default), layout classes, typography, print styles |
| Assets: `/assets/` | Images (profile avatar), JavaScript (PDF export logic), favicons |
| Build config: `_config.yml` | Jekyll site settings, URL routing, compression, encoding |
| Build: `Gemfile` | Ruby dependencies (jekyll, bundler) |

## Key design decisions

- **Single source of truth in YAML:** All resume content lives in `_data/data.yml`, decoupling content from presentation. Updating the resume requires editing only one file; templates regenerate automatically on build.
- **Static site generation over a CMS:** Jekyll with GitHub Pages eliminates server-side dependencies, reduces attack surface, and provides zero-cost hosting with custom domain support — appropriate for a personal career site.
- **Token-based SCSS theming with dark mode default:** The SCSS layer uses design tokens for colors and typography, making theme changes low-risk and enabling print-specific styles without duplicating layout logic.

## Integration points

- **GitHub Pages:** Hosts the built `_site/` output at `career.secop.dev` via a custom domain CNAME. CI/CD is handled by GitHub Actions or Pages auto-build on push to the tracked branch.
- **PDF export:** Client-side JavaScript in `/assets/` drives a browser print-to-PDF flow for resume download — no server or external API dependency.
- **No external data APIs:** The site is fully self-contained; all content is static YAML with no runtime data fetching.

## Platform constraints

- Ruby 3.x + Jekyll 4.x required for build.
- Node.js optional for development tooling only.
- Hosted on GitHub Pages with custom domain (`career.secop.dev`).
- Content source of truth: `_data/data.yml` (single file).
- Rendering engine: Liquid (Jekyll template language).
- Development server: `bundle exec jekyll serve` (port 4000).
- Build output: `_site/` (static HTML, gitignored).
