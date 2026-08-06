# External Integrations

**Analysis Date:** 2026-08-05

## APIs & External Services

**Time/Timezone:**
- World Time API (worldtimeapi.org) - Provides timezone display link in sidebar contact section
  - Referenced in: `_includes/contact.html` line 36
  - Used for: Timezone link generation (informational only, no API call required)

**Fonts:**
- Google Fonts API - Supplies web fonts (Inter and JetBrains Mono)
  - Referenced in: `_includes/head.html` lines 84-86
  - URL: `https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap`
  - Auth: None required

## Data Storage

**Databases:**
- Not applicable - static site (no database)

**File Storage:**
- GitHub Pages - Static site hosting and content delivery
- Local filesystem - Resume data stored in `_data/data.yml` and assets in `assets/` directory

**Caching:**
- Jekyll build-time caching via jekyll-include-cache plugin
- Browser caching via standard HTTP headers

## Authentication & Identity

**GitHub Integration:**
- GitHub Pages - Hosting and automatic build/deployment
  - Metadata integration: jekyll-github-metadata plugin enabled
  - Used for: Repository metadata injection during site generation
- GitHub Gist Embedding - jekyll-gist plugin (0.5.0) for potential gist embeds
  - Not currently used in data but available for markdown content

**Social Media Links:**
- LinkedIn, GitHub, Twitter, Bluesky, Mastodon, GitLab, Bitbucket, Stack Overflow, CodeWars, HackerRank, LeetCode, Goodreads
- Implementation: Simple HTML links generated from `_data/data.yml` entries
- Referenced in: `_includes/contact.html`
- No authentication or API integration - statically linked profiles

## Monitoring & Observability

**Error Tracking:**
- Not detected

**Logs:**
- Jekyll development server logs (output to console in docker-compose setup)
- No external logging service configured

**Analytics:**
- Not detected - no Google Analytics, Mixpanel, or other tracking service configured
- Google Site Verification support configured in `_config.yml` (line 12) but not currently set

## CI/CD & Deployment

**Hosting:**
- GitHub Pages - Automatic build and deployment on push
- Custom domain: career.secop.dev (CNAME: `CNAME` file)

**CI Pipeline:**
- GitHub Pages automatic build (Jekyll)
- No custom GitHub Actions workflows detected (`.github/` contains only `FUNDING.yml`)
- Docker Compose for local development testing

## Environment Configuration

**Required env vars:**
- `JEKYLL_VERSION` - Set to 3.8 in docker-compose.yml
- No other custom environment variables required for deployment

**Secrets location:**
- Not applicable - static site with no secrets
- All configuration is checked into git

## Webhooks & Callbacks

**Incoming:**
- GitHub push webhook (automatic, managed by GitHub Pages)

**Outgoing:**
- None detected

## CDN & External Resources

**Content Delivery:**
- Google Fonts - Served from googleapis.com and gstatic.com
  - Preconnect: `https://fonts.googleapis.com` and `https://fonts.gstatic.com`
  - Reference: `_includes/head.html` lines 84-86

**Icons & Assets:**
- Font Awesome icons (locally bundled in `assets/plugins/font-awesome/`)
- Bootstrap CSS/JS (locally bundled in `assets/plugins/bootstrap/`)
- No external CDN links for Bootstrap or Font Awesome

## Metadata & SEO

**Structured Data:**
- Schema.org Person schema embedded as JSON-LD in `_includes/head.html` (lines 43-81)
- OpenGraph meta tags for social sharing
- Twitter Card meta tags for Twitter sharing
- Keywords, description, and canonical URL metadata configured

**SEO Plugins:**
- jekyll-seo-tag - Generates SEO metadata and tags
- jekyll-sitemap - Generates sitemap.xml for search engines

---

*Integration audit: 2026-08-05*
