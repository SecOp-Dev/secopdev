# secopdev-resume

## What This Is

A Jekyll-based static personal resume/CV site for Sean Cullen (Cloud Security Engineer & Architect), published at `career.secop.dev` via GitHub Pages with a custom domain. It renders resume content driven by `_data/data.yml` into a themed, printable single-page site.

## Core Value

The published site at career.secop.dev must always build cleanly and accurately represent Sean's current professional profile — it is a live, externally-shared career artifact (used for job applications, recruiter links, and networking), so broken builds or stale/incorrect content directly cost professional credibility.

## Business Context

- **Customer**: Sean Cullen (site owner) and anyone he shares the link with (recruiters, hiring managers, professional contacts)
- **Revenue model**: N/A — personal career-advertisement asset, not monetized
- **Success metric**: Site builds and deploys without error; content stays current and accurate; site loads fast and looks credible/professional
- **Strategy notes**: N/A

## Requirements

### Validated

- ✓ Static resume site renders from `_data/data.yml` via Jekyll includes/layouts — existing
- ✓ Custom domain (`career.secop.dev`) served via GitHub Pages (`CNAME`) — existing
- ✓ Print-friendly layout (`print.html`) for PDF/paper export — existing
- ✓ SEO metadata, sitemap, RSS feed via jekyll-seo-tag/jekyll-sitemap/jekyll-feed — existing
- ✓ Dark/light theme toggle — existing
- ✓ Local Docker-based dev environment (`docker-compose.yml`) — existing

### Active

- [ ] Add CI validation (GitHub Actions: `jekyll build` + YAML lint) so bad commits never reach the deployed site
- [ ] Update outdated Docker/Jekyll dev environment (`jekyll/builder:4.0` + `JEKYLL_VERSION: 3.8`) to a current, supported version
- [ ] Add basic security headers and fix `rel="noopener noreferrer"` gaps on external links (reverse-tabnabbing risk)
- [ ] Remove or replace the external `worldtimeapi.org` dependency in the contact section (single point of failure for a live page)
- [ ] Trim unused/duplicate frontend assets (unminified Font Awesome/Bootstrap variants, IE8/9 polyfills) to improve load performance

### Out of Scope

- Full framework migration (e.g. off Jekyll to Next.js/Astro) — site works, high effort for low marginal value on a resume site
- Analytics/monitoring platform — static resume site has no meaningful traffic-driven decisions to make
- Automated visual regression / accessibility test suites — disproportionate tooling investment for a single-maintainer personal site; manual review is sufficient at this scale

## Context

- Actively maintained personal-brand asset under `pro/` (professional-advertisement category) in the `~/dev` fleet workspace, migrated from `/mnt/q` as part of a broader repo-fleet standardization effort.
- Content edits happen almost entirely through `_data/data.yml`; layout/include changes are rare.
- No test infrastructure or CI pipeline exists today (confirmed via codebase mapping, see `.planning/codebase/CONCERNS.md`) — the Active requirements above target the highest-ROI gaps identified there (build validation, security, dependency currency, performance), not a full rewrite.
- Full technical concerns audit (tech debt, bugs, security, performance, fragile areas, scaling limits): `.planning/codebase/CONCERNS.md`.

## Constraints

- **Tech stack**: Jekyll (Ruby) + GitHub Pages hosting — locked in by existing deployment (custom domain, `github-pages` gem); any stack change is a major decision, not incidental
- **Compatibility**: Must remain buildable within GitHub Pages' supported Jekyll/plugin versions if GitHub Pages hosting continues
- **Maintainer bandwidth**: Single maintainer, personal project — prefer low-maintenance fixes (CI checks, config changes) over new tooling/process overhead

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Onboard via GSD without a rewrite | Site is functional and shipped; the value is closing maintenance/security gaps, not re-platforming | — Pending |
| Scope Active requirements from CONCERNS.md audit rather than a fresh feature list | Codebase map already surfaced concrete, evidence-backed gaps; reuse that instead of re-deriving from scratch | — Pending |

---
*Last updated: 2026-08-05 after initialization*

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state
