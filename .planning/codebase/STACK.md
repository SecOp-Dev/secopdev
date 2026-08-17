# Technology Stack

**Analysis Date:** 2026-08-05

## Languages

**Primary:**
- Ruby 3.x - Runtime for Jekyll and gem dependencies

## Runtime

**Environment:**
- Ruby (via docker image jekyll/builder:4.0)
- Jekyll version 3.8 in container (via JEKYLL_VERSION env var in `docker-compose.yml`)

**Package Manager:**
- Bundler 2.3.19
- Lockfile: `Gemfile.lock` (present)

## Frameworks

**Core:**
- Jekyll 3.9.5 - Static site generator for resume/personal site
- GitHub Pages 231 - GitHub Pages compatibility gem that bundles Jekyll and plugins

**Build/Dev:**
- Sass 3.7.4 - CSS preprocessing (configured in `_config.yml`)
- Docker 4.0 - Container orchestration via `docker-compose.yml` for local development

## Key Dependencies

**Critical:**
- jekyll 3.9.5 - Site generation and development server
- github-pages 231 - Ensures compatibility with GitHub Pages build environment
- webrick 1.9.1 - Web server for local development (required for Jekyll serve on Ruby 3+)

**Frontend/Styling:**
- Bootstrap (locally bundled) - CSS framework for responsive layout
- Font Awesome (locally bundled) - Icon library for social links and UI elements
- Sass - CSS compilation with compression

**Jekyll Plugins (via github-pages):**
- jekyll-feed 0.17.0 - RSS feed generation
- jekyll-seo-tag 2.8.0 - SEO metadata generation
- jekyll-sitemap 1.4.0 - Sitemap generation
- jekyll-github-metadata 2.16.1 - GitHub metadata integration
- jekyll-mentions 1.6.0 - @mention link conversion
- jekyll-redirect-from 0.16.0 - Redirect handling
- jekyll-paginate 1.1.0 - Pagination support
- jekyll-gist 1.5.0 - GitHub Gist embedding
- jekyll-relative-links 0.6.1 - Relative link processing
- jekyll-include-cache 0.2.1 - Include caching for performance
- jekyll-avatar 0.8.0 - GitHub avatar integration
- jekyll-coffeescript 1.2.2 - CoffeeScript support
- jekyll-default-layout 0.1.5 - Default layout assignment
- jekyll-optional-front-matter 0.3.2 - YAML frontmatter handling
- jekyll-readme-index 0.3.0 - README indexing
- jekyll-remote-theme 0.4.3 - Remote theme loading
- jemoji 0.13.0 - Emoji support

**Utilities:**
- kramdown 2.4.0 - Markdown parser (GFM variant)
- commonmarker 0.23.11 - CommonMark parser for markdown processing
- rouge 3.30.0 - Syntax highlighting for code blocks
- liquid 4.0.4 - Templating engine
- octokit 4.25.1 - GitHub API client (used by jekyll-github-metadata)

## Configuration

**Environment:**
- `_config.yml` - Main Jekyll configuration
  - Site title, URL (https://career.secop.dev)
  - SASS compilation settings (compressed output)
  - Port: 4000
  - Host: 0.0.0.0
  - Content compression enabled
  - Docs directory excluded from build

**Build:**
- `docker-compose.yml` - Docker configuration for local development
  - Image: jekyll/builder:4.0
  - Command: `jekyll serve --watch --force_polling --verbose --livereload --host 0.0.0.0`
  - Mounts current directory to `/srv/jekyll` in container
  - Port mapping: 4000:4000
- `Gemfile` - Ruby dependencies specification
- `Gemfile.lock` - Pinned dependency versions

**Custom Domain:**
- `CNAME` file contains `career.secop.dev` for GitHub Pages custom domain

## Platform Requirements

**Development:**
- Docker and docker-compose (for containerized Jekyll environment)
- Ruby 3.x (if running without Docker)
- Bundler for gem management

**Production:**
- GitHub Pages hosting (implied by github-pages gem and CNAME configuration)
- Custom domain: career.secop.dev (DNS CNAME or A record configuration required)

---

*Stack analysis: 2026-08-05*
