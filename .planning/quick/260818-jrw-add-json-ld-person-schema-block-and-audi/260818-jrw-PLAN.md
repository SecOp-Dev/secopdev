---
quick_id: 260818-jrw
status: complete
---

# Quick Task: JSON-LD block + /print CSS audit

Follow-up to `.planning/research/ats-optimization-research.md`. Two asks:

1. Add the `schema.org/Person` JSON-LD block to the live page's `<head>`.
2. Audit `/print`'s CSS for ATS single-column / real-anchor-link compliance and
   fix anything found.

## Task 1 — JSON-LD block

**Finding:** already present. `_includes/head.html` (lines 38-81) contains a
`schema.org/Person` JSON-LD block sourced from `_data/data.yml` (name, jobTitle,
description, url, image, contact, worksFor, alumniOf, sameAs → LinkedIn/GitHub,
knowsAbout from skills.categories, hasCredential from certifications.list).
`jobTitle` is hardcoded to "Security Analyst II" — verified against
`_data/data.yml`'s first (current, 2026-Present) experience entry, matches.
No change needed.

## Task 2 — /print CSS audit

**Finding:** real gap. `.wrapper` uses CSS Grid (`_sass/_base.scss`,
`grid-template-columns: repeat(10, 1fr)`, sidebar spans 3 / main spans 7,
visual order controlled by `$sidebar-order`/`$resume-order` set from
`data.sidebar.position` in `assets/css/main.scss`). The mobile breakpoint
(`@media (max-width: 767px)`, `_sass/_responsive.scss`) already linearizes this
to `display: block`, but `_sass/_print.scss`'s `@media print` block never did —
so a browser print-to-PDF of `/print` rendered two visual columns side by side,
the exact multi-column layout the research flagged as the most damaging pattern
for ATS text extraction (columns interleave when a parser reads left-to-right).

Links in `_includes/sidebar.html`/`contact.html` were not re-audited line by
line this pass — spot check during research found real `<a href>` tags, not
icon-only links; flag as a follow-up if a deeper anchor-tag audit is wanted.

**Fix:** added `display: block;` to `.wrapper` inside `_sass/_print.scss`'s
`@media print` block, mirroring the existing mobile pattern. Since `order` and
`grid-column` are grid/flex-item-only properties, they become no-ops once the
parent isn't a grid — print output now falls back to strict DOM order
(sidebar content, then main resume content) regardless of the on-screen
`sidebar.position` setting. Verified via `bundle exec jekyll build`: compiled
`_site/assets/css/main.css`'s `@media print` block now includes
`.wrapper{display:block;...}`, and `_site/print.html`'s JSON-LD block renders
correctly with the confirmed `jobTitle`.
