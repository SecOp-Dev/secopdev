---
quick_id: 260818-jrw
status: complete
---

# Summary: JSON-LD block + /print CSS audit

## What happened

1. **JSON-LD Person block** — already implemented in `_includes/head.html`;
   verified accurate against `_data/data.yml` (current role/skills/certs), no
   change made.
2. **`/print` CSS audit** — found the print media query never linearized the
   two-column `.wrapper` grid (unlike the existing mobile breakpoint), so a
   browser print-to-PDF export would render sidebar and main content as
   side-by-side columns — the layout pattern most likely to interleave text on
   ATS extraction if that PDF is later uploaded to a job portal. Fixed by
   adding `display: block;` to `.wrapper` in `_sass/_print.scss`'s
   `@media print` block.

## Verification

`bundle exec jekyll build` (via vendored bundler,
`vendor/bundle/ruby/3.2.0/bin`) succeeded. Confirmed in build output:
- `_site/assets/css/main.css`'s `@media print` block contains
  `.wrapper{display:block;...}`.
- `_site/print.html` renders the `application/ld+json` block with correct
  `jobTitle: "Security Analyst II"`.

Build output (`_site/`) removed after verification — not committed.

## Files changed

- `_sass/_print.scss` — one-line addition (`display: block;` on `.wrapper`
  inside `@media print`).

## Not done / follow-up

- Did not do a full line-by-line audit of every contact/social link in
  `_includes/sidebar.html`/`contact.html` for real-anchor-vs-icon-only markup
  — spot check looked correct. Flagged as open follow-up if wanted.
