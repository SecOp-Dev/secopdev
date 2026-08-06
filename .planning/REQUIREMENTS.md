# Requirements

Source: `.planning/PROJECT.md` Active requirements, scoped from the codebase concerns audit at `.planning/codebase/CONCERNS.md`. This is a brownfield maintenance/hardening pass on an already-shipped Jekyll resume site (`career.secop.dev`), not a greenfield feature build.

## v1 Requirements

### Build Reliability

- [ ] **BUILD-01**: A GitHub Actions workflow runs `jekyll build` on every push/PR so a broken build is caught before it can reach the deployed site
- [ ] **BUILD-02**: The CI workflow lints `_data/data.yml` (or otherwise validates its YAML syntax) so a malformed content edit fails CI instead of silently breaking a rendered section

### Environment Currency

- [ ] **ENV-01**: The local Docker dev environment uses a current, supported Jekyll/Ruby image instead of the pinned 2020-era `jekyll/builder:4.0` + `JEKYLL_VERSION: 3.8`
- [ ] **ENV-02**: The Ruby version required by the project is declared explicitly (Gemfile `ruby` constraint and/or `.ruby-version`) so local and CI environments can't silently diverge

### Security Hardening

- [ ] **SEC-01**: Every external link using `target="_blank"` includes `rel="noopener noreferrer"` to close the reverse-tabnabbing gap
- [ ] **SEC-02**: Basic security headers (CSP, X-Frame-Options, X-Content-Type-Options) are configured for the deployed site

### Reliability / Dependencies

- [ ] **REL-01**: The contact section no longer depends on the external `worldtimeapi.org` service as a single point of failure — timezone is rendered as static/local text instead

### Performance

- [ ] **PERF-01**: Duplicate/unminified vendor CSS (unminified Font Awesome, unused Bootstrap subset) is trimmed or replaced with the minified variant already available in the repo
- [ ] **PERF-02**: Obsolete IE8/IE9 polyfills (`html5shiv`, `respond.js`) are removed from `_includes/head.html`

## v2 Requirements (Deferred)

- **CI-EXT-01**: Add automated link-checking (e.g. `htmlproofer`) to CI — deferred until BUILD-01/02 land and prove the CI pipeline is worth extending
- **PERF-EXT-01**: Full asset audit / tree-shake of Bootstrap and Font Awesome (only the icons actually used) — bigger effort than the immediate PERF-01 cleanup, revisit after basic trimming ships
- **ABOUT-01**: Fix or remove the dead "How to use?" link in `_includes/about.html` — currently disabled in `_data/data.yml` (sidebar.about: False), so not user-visible; low priority until that section is re-enabled

## Out of Scope

- Full framework migration off Jekyll (e.g. to Next.js/Astro) — the current stack works and is GitHub Pages-compatible; re-platforming has high effort and no clear return for a personal resume site
- Analytics/monitoring platform (Plausible, Fathom, etc.) — no traffic-driven decisions are made from this site; adding a tracking dependency isn't worth it
- Automated visual regression or accessibility test suites — disproportionate tooling investment for a single-maintainer, low-change-frequency personal site; manual review covers this at current scale

## Traceability

<!-- Filled in by roadmapper during phase creation -->

| Requirement | Phase |
|-------------|-------|
| (pending roadmap creation) | — |
