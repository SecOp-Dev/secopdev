# secopdev-resume — Project Tracker

Own-content (no `.planning/` in this repo). Grounded in
`.planning/phases/03-tracker-vocabulary-rollout/fact-sheets/2.0-career__secopdev-resume.md`
(rollout project, external to this repo). Consolidation with `sean-cullen-professional-site` is
decided but not yet executed (see Blocked/Next Action) — this tracker is authored now regardless.

## Objective

This repo is the online resume for Sean Cullen, a cloud security engineer/architect targeting
Director/Lead Security Operations, Detection Engineering, and Cloud Security Architect roles. The
site is hosted at career.secop.dev and built with Jekyll.

## Problem

Jekyll static site, LIVE at career.secop.dev, content from `_data/data.yml`. Solves the need for a
live, ATS-parseable, metrics-forward public resume site for senior cloud-security role applications.

## Status

Active, live. Currently the canonical published resume — but a documented, already-decided
(2026-06-16) plan on `sean-cullen-professional-site` names that Astro rebuild as this repo's
successor. Not yet executed.

## Blocked

Redundant with `sean-cullen-professional-site` — both publish the same resume content on two stacks.
A 2026-06-16 plan on `sean-cullen-professional-site` already names Astro as survivor but the
DNS/retirement cutover was never executed.

## Next Action

Hold as live canonical site until `sean-cullen-professional-site` clears its deploy gate, then
execute the planned cutover (DNS + freeze/retire this repo).

## Next Command

```
cat /mnt/q/2.0-career/sean-cullen-professional-site/deploy/README.md
```

## How-to Guides

- Serve locally: `bundle exec jekyll serve`, then browse `localhost:4000` to verify layout, section
  rendering, and no YAML parse errors.
- Validate `_data/data.yml` without Jekyll: `ruby -e "require 'yaml';
  YAML.load_file('_data/data.yml'); puts 'OK'"`.
- Content source of truth for quantified metrics: `Sean Cullen - Resume.docx` in the repo root —
  cross-reference before adding/changing achievement bullets.

## Uses

No external third-party API/service runtime coupling — static Jekyll site with client-side PDF
export (`assets/js/pdf-generator.js`, browser print-to-PDF), no live API calls.

## Related Projects

### sean-cullen-professional-site

Lineage/successor relationship, no deploy-time coupling: a documented, already-decided (2026-06-16)
plan on `sean-cullen-professional-site` names that Astro rebuild as this repo's successor. Execution
is blocked on `sean-cullen-professional-site`'s deploy gate, not on re-deciding which site should
survive.

### resume-data

Upstream data-provenance relationship, no automated deploy-time coupling: should pull career facts
from `resume-data`'s `career_data_raw.json`/`soul_outline.json` as source of truth, but does not do
so automatically today — content is manually copied.
