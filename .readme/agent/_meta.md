---
type: project
title: "secopdev-resume"
aliases: ["career-resume-site"]
tags:
  - type/project
  - tier/2.0-career
  - tool/secopdev-resume
  - capability/portfolio-site
  - topic/career
status: active
created: 2026-07-02
updated: 2026-07-02
related: ["sean-cullen-professional-site", "resume"]
repo: "https://github.com/SecOp-Dev/secopdev"
branch: master
live: true
rag-flag: "yellow"
blockers: ["redundant with sean-cullen-professional-site — both publish the same resume content on two stacks; a 2026-06-16 plan on sean-cullen-professional-site already names Astro as survivor but the DNS/retirement cutover was never executed"]
next-action: "hold as live canonical site until sean-cullen-professional-site clears its deploy gate, then execute the planned cutover (DNS + freeze/retire this repo)"
next-command: "cat /mnt/q/2.0-career/sean-cullen-professional-site/deploy/README.md"
last-documented-sha: ""
tier: "2.0-career"
docs: ".readme/agent/"
---
<!-- frontmatter anchor for kb-sync harvest. Do not rename this file. -->

Jekyll static site, LIVE at career.secop.dev, content from `_data/data.yml`. Currently the canonical published resume — but a documented, already-decided (2026-06-16) plan on `sean-cullen-professional-site` names that Astro rebuild as this repo's successor. Not yet executed: DNS cutover and this repo's retirement are pending, gated on the other repo's Cloudflare Tunnel + content sign-off, not on any open question in this repo itself. See [[career-tier-resume-consolidation]] memory for the full finding.
