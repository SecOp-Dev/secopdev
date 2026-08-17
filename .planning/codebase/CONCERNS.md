# Codebase Concerns

**Analysis Date:** 2026-08-05

## Tech Debt

**Outdated Jekyll Docker environment:**
- Issue: Docker Compose specifies `jekyll/builder:4.0` (2020) with `JEKYLL_VERSION: 3.8` (very outdated)
- Files: `docker-compose.yml` (lines 4, 9)
- Impact: Security vulnerabilities in dependencies, missed performance improvements, compatibility issues with modern plugins
- Fix approach: Update to latest stable Jekyll image (`jekyll/builder:latest` or pinned current version) and remove hardcoded JEKYLL_VERSION to use image default

**No Ruby version constraint:**
- Issue: `Gemfile` does not specify Ruby version; no `.ruby-version` file exists
- Files: `Gemfile`, repo root missing `.ruby-version`
- Impact: Team members and CI may use different Ruby versions, causing inconsistent builds
- Fix approach: Add `ruby '~> 3.2'` (or appropriate version) to Gemfile and create `.ruby-version` file

**Force polling enabled in development:**
- Issue: `docker-compose.yml` line 5 uses `--force_polling` flag
- Files: `docker-compose.yml`
- Impact: Excessive CPU/disk usage during development; inefficient on modern file systems
- Fix approach: Remove `--force_polling` flag; use native file watchers instead

**No Docker resource limits:**
- Issue: `docker-compose.yml` does not set CPU or memory limits
- Files: `docker-compose.yml`
- Impact: Container can consume unlimited resources; may crash or degrade host system
- Fix approach: Add `deploy.resources.limits` (CPU, memory) and `deploy.resources.reservations` sections

**Missing security headers configuration:**
- Issue: No Content-Security-Policy, X-Frame-Options, X-Content-Type-Options, or other security headers configured
- Files: `_config.yml` (no header configuration), server config not defined
- Impact: Site vulnerable to XSS, clickjacking, and content-sniffing attacks
- Fix approach: Configure security headers via `_headers` file (for Netlify) or HTTP server directives (nginx/Apache)

## Known Bugs

**Empty href in about section:**
- Symptoms: "How to use?" link in sidebar has empty href and opens nowhere
- Files: `_includes/about.html` (line 6)
- Trigger: Click the "How to use?" link when about section is enabled (currently disabled in sidebar settings)
- Workaround: Section is disabled by default in `_data/data.yml` (sidebar.about: False)
- Fix approach: Either remove the link entirely or point it to actual documentation

**External dependency failure point:**
- Symptoms: Timezone display breaks if worldtimeapi.org is unreachable
- Files: `_includes/contact.html` (line 36) — links to `https://worldtimeapi.org/timezone/...`
- Trigger: Service goes down or returns error
- Workaround: Remove timezone link or display timezone as text-only (no link)
- Fix approach: Remove external API dependency; render timezone as static text or local time via client-side JS

## Security Considerations

**Missing `rel="noopener noreferrer"` on external links:**
- Risk: All `target="_blank"` links are vulnerable to reverse tabnabbing attacks; opener window can be redirected
- Files: Multiple files with external links missing `rel` attribute:
  - `_includes/contact.html` (lines 36, 42, 48, 54, 60, 66, 72, 78, 84, 91, 98, 104, 110, 116, 122, 128, 134)
  - `_includes/oss-contributions.html` (line 20)
  - `_includes/certifications.html` (line 34)
- Current mitigation: None
- Recommendations: Add `rel="noopener noreferrer"` to all `<a target="_blank">` tags; consider using Liquid filter to automate this

**Inline JavaScript in head tag:**
- Risk: Theme storage/retrieval via localStorage increases attack surface for XSS
- Files: `_includes/head.html` (line 3) — inline script before CSP can be properly configured
- Current mitigation: Script is simple and local-only; no external data
- Recommendations: Move to external file with CSP nonce or hash; ensure Content-Security-Policy header is configured

**Publicly exposed contact information:**
- Risk: Phone number, email, and social media handles are in plain HTML source
- Files: `_data/data.yml` (lines 16-17), rendered in `_includes/contact.html`
- Current mitigation: This is a public resume site; information is intentional
- Recommendations: Monitor for scraping/spam; consider email obfuscation if spam becomes issue

**Old browser support artifacts:**
- Risk: IE8/IE9 polyfills in head.html are outdated and may contain unpatched vulnerabilities
- Files: `_includes/head.html` (lines 100-103) — html5shiv, respond.js
- Current mitigation: Modern browsers ignore these
- Recommendations: Remove IE8/IE9 conditional comments; these browsers have zero market share and are unsafe

## Performance Bottlenecks

**Large unoptimized vendor assets:**
- Problem: Bootstrap.css (144KB) and Font-Awesome all.css (140KB) are loaded uncompressed; Font Awesome metadata files are 213KB total
- Files: 
  - `assets/plugins/bootstrap/css/bootstrap.css`
  - `assets/plugins/font-awesome/css/all.css`
  - `assets/plugins/font-awesome/metadata/icons.json` (96KB), `icon-families.json` (116KB)
- Cause: Full framework loaded instead of minimal subset; metadata files not minified or pruned
- Improvement path:
  1. Audit which Font Awesome icons are actually used; consider custom icon build or SVG sprite
  2. Tree-shake Bootstrap to include only used components
  3. Remove Font Awesome metadata files if not used in frontend
  4. Enable gzip compression in Jekyll config or server

**Unminified CSS in production:**
- Problem: Jekyll compress.html layout is available but not used; CSS is large
- Files: `_config.yml` (compress-site: yes), `_layouts/compress.html` (exists but not referenced in index.html)
- Cause: Main page (`index.html`) does not use compress layout; only print.html might
- Improvement path: Ensure production build applies `layout: compress` to reduce final HTML; minify CSS via jekyll-sass-processor

**Multiple CSS variants loaded:**
- Problem: Both minified and full versions of CSS exist; unclear which is loaded
- Files: `assets/plugins/font-awesome/css/` contains `all.css` (140KB) and `all.min.css` (96KB)
- Cause: Likely both versions were committed; only minified should be in production
- Improvement path: Remove non-minified CSS files; ensure `.gitignore` excludes them if generated

## Fragile Areas

**Jekyll data structure brittleness:**
- Files: `_data/data.yml` (100+ lines, deeply nested YAML)
- Why fragile: YAML is indentation-sensitive; comment in file warns "even a small syntax error here can lead to failures in output"; no validation in CI
- Safe modification: 
  1. Use a YAML linter in CI (yamllint or similar)
  2. Test build locally with `jekyll build` before committing
  3. Consider splitting large data.yml into multiple files per section
- Test coverage: No schema validation; Jekyll silently ignores malformed YAML

**Liquid template conditionals:**
- Files: All `_includes/*.html` extensively use `{% if ... %}` to render sections based on data.yml presence
- Why fragile: Typo in data key name silently hides section; no validation that key name matches template expectation
- Safe modification: Add comment at top of each include showing expected data.yml keys; add CI check (grep/regex) to verify keys exist
- Test coverage: Visual testing only; no automated checks

**Social media URL construction:**
- Files: `_includes/contact.html` (lines 52-131) construct social media URLs by appending username to hardcoded base URL
- Why fragile: If social platform changes URL structure (GitHub did: github.com → github.com/user), links break; no validation that usernames are valid
- Safe modification: 
  1. Add a note in CLAUDE.md if platform URL structure changes
  2. Consider using a single source of truth (e.g., config that maps platform → URL template)
- Test coverage: No automated link validation; broken links only discovered visually

## Scaling Limits

**No CI/CD pipeline:**
- Current capacity: Manual testing and deployment; no automated builds or checks
- Limit: As content grows (more experiences, projects, recommendations), manual validation error risk increases
- Scaling path:
  1. Add GitHub Actions workflow for `jekyll build` validation on every commit
  2. Add YAML linting step
  3. Add link checking step (for broken internal/external links)

**No monitoring or logging:**
- Current capacity: Site is static; no backend logs
- Limit: Cannot detect if users are hitting errors, broken links, or 404s
- Scaling path: Add analytics (Plausible, Fathom) for traffic/error visibility; configure server-side 404 handling

## Dependencies at Risk

**Gemfile.lock with old entries:**
- Risk: Gemfile specifies `github-pages` which pins Jekyll and plugins to GitHub Pages versions; these may lag behind upstream
- Impact: Cannot use newer Jekyll features or fixes; potential security vulnerabilities if github-pages doesn't update timely
- Migration plan: Evaluate whether `github-pages` gem is necessary (only needed if deploying to GH Pages). If using custom domain + external host, switch to pinning jekyll + plugins independently with `Gemfile` version constraints

**Font Awesome 6.x with v4 shims:**
- Risk: `v4-shims.css` indicates legacy v4 compatibility mode; adds bloat
- Impact: File size bloat; future deprecation of shims will require rework
- Migration plan: Audit all Font Awesome icons used in site; ensure they exist in v6 with v6 syntax; remove v4-shims files from build

**Bootstrap 3/4 unclear:**
- Risk: Bootstrap version not explicitly stated in Gemfile; unclear which version is in assets/
- Impact: Responsive behavior, class names, and JavaScript plugins may differ from assumptions
- Migration plan: Declare Bootstrap version in README; verify consistency between assets and documentation; consider upgrading to Bootstrap 5 if feasible

## Missing Critical Features

**No build validation in version control:**
- Problem: Jekyll build is not run in CI; commits could introduce syntax errors that only fail on deploy
- Blocks: Catching errors early; preventing broken deployments

**No accessibility testing:**
- Problem: No automated a11y checks (WCAG 2.1 compliance, color contrast, heading hierarchy)
- Blocks: Ensuring site is usable for users with disabilities; meeting legal/compliance requirements

**No performance testing:**
- Problem: No Lighthouse CI, PageSpeed monitoring, or Core Web Vitals tracking
- Blocks: Detecting regressions in load time, CLS, LCP as content grows

## Test Coverage Gaps

**Zero automated tests:**
- What's not tested: YAML syntax, Jekyll build success, link validity, image optimization, security headers, accessibility
- Files: Entire codebase lacks test infrastructure; no `Rakefile`, no GitHub Actions workflows
- Risk: Broken builds, dead links, and configuration errors only discovered manually
- Priority: High — Jekyll builds are deterministic and testable; investment in CI validation has high ROI

**No visual regression testing:**
- What's not tested: Responsive design (mobile/tablet/desktop), theme toggle functionality, print layout
- Risk: CSS changes could break layout on specific screen sizes; no automated detection
- Priority: Medium — manual testing covers this but slow and error-prone

**No link validation:**
- What's not tested: External links (LinkedIn, GitHub, etc.) are valid; internal links don't 404
- Risk: Dead links damage credibility; broken social links hide professional profiles
- Priority: Medium — can be added to CI with minimal effort (htmlproofer gem)

---

*Concerns audit: 2026-08-05*
