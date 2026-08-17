# Roadmap: secopdev-resume

## Overview

This is a brownfield maintenance/hardening pass on an already-shipped Jekyll resume site (`career.secop.dev`), not a greenfield build. The nine v1 requirements come directly from the codebase concerns audit (`.planning/codebase/CONCERNS.md`) and cluster into three natural delivery boundaries: first, put a current environment and a CI safety net in place so future changes are protected against broken builds and malformed data; then close the two concrete security gaps (reverse-tabnabbing links, missing HTTP security headers); then remove the external single point of failure and trim bloated/obsolete frontend assets for a faster, more reliable page. Each phase is independently verifiable and leaves the live site in a working, deployed state.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Environment & Build Reliability** - Current dev environment, explicit Ruby version, and CI that catches broken builds/bad YAML before they reach the deployed site
- [ ] **Phase 2: Security Hardening** - Close reverse-tabnabbing gap on external links and add basic HTTP security headers
- [ ] **Phase 3: Reliability & Performance Cleanup** - Drop the external timezone-API dependency and trim duplicate/obsolete frontend assets

## Phase Details

### Phase 1: Environment & Build Reliability
**Goal**: The project builds on a current, explicitly-declared environment, and CI catches a broken build or malformed content edit before it can reach the deployed site.
**Mode:** mvp
**Depends on**: Nothing (first phase)
**Requirements**: ENV-01, ENV-02, BUILD-01, BUILD-02
**Success Criteria** (what must be TRUE):
  1. Local Docker dev environment runs a current, supported Jekyll/Ruby image instead of the pinned 2020-era `jekyll/builder:4.0` + `JEKYLL_VERSION: 3.8`
  2. The Ruby version required by the project is declared explicitly (Gemfile `ruby` constraint and/or `.ruby-version`) so local and CI environments can't silently diverge
  3. A GitHub Actions workflow runs `jekyll build` on every push/PR and fails the check when the build breaks
  4. That same CI workflow validates `_data/data.yml` YAML syntax and fails when content is malformed
**Plans**: TBD

### Phase 2: Security Hardening
**Goal**: External links and the deployed site close the two concrete security gaps surfaced in the concerns audit — reverse-tabnabbing on `target="_blank"` links and missing baseline HTTP security headers.
**Mode:** mvp
**Depends on**: Phase 1
**Requirements**: SEC-01, SEC-02
**Success Criteria** (what must be TRUE):
  1. Every external link using `target="_blank"` includes `rel="noopener noreferrer"`
  2. The deployed site sends basic security headers (CSP, X-Frame-Options, X-Content-Type-Options)
**Plans**: TBD
**UI hint**: yes

### Phase 3: Reliability & Performance Cleanup
**Goal**: The site drops its external single point of failure and its duplicate/obsolete frontend assets, so it loads faster and has one fewer thing that can silently break it.
**Mode:** mvp
**Depends on**: Phase 1
**Requirements**: REL-01, PERF-01, PERF-02
**Success Criteria** (what must be TRUE):
  1. The contact section's timezone displays as static/local text and no longer links to or depends on `worldtimeapi.org`
  2. Duplicate/unminified vendor CSS (unminified Font Awesome, unused Bootstrap subset) is trimmed to the minified variant already available in the repo
  3. Obsolete IE8/IE9 polyfills (`html5shiv`, `respond.js`) are removed from `_includes/head.html`
**Plans**: TBD
**UI hint**: yes

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3

| Phase | Plans Complete | Status | Completed |
|-------|-----------------|--------|-----------|
| 1. Environment & Build Reliability | 0/TBD | Not started | - |
| 2. Security Hardening | 0/TBD | Not started | - |
| 3. Reliability & Performance Cleanup | 0/TBD | Not started | - |
