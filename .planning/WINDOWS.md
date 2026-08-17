---
schema_version: 1
open_count: 1
waived_count: 0
fixed_count: 0
total_count: 1
last_updated: 2026-08-17T19:53:55.145Z
---

# Broken Windows Ledger

> Cross-phase defect register. With `workflow.windows_enforce` enabled, `/gsd-ship` blocks while `open_count > 0`.
> Waive with `gsd-tools windows waive <id> "<reason>"` (reason required).
> Mark fixed with `gsd-tools windows fixed <id>`.

| id | phase | kind | file | line | description | status | reason | recorded_at | resolved_at |
|----|-------|------|------|------|-------------|--------|--------|-------------|-------------|
| 1 | quick-260817-kkj | unrun-verify | assets/images/headshot.jpg |  | Task 2's automated <verify> (bundle exec jekyll build) could not run — Ruby/Bundler/Jekyll are not installed in this execution environment. Manual/CI build verification recommended before this lands on the live site. | open |  | 2026-08-17T19:53:55.145Z |  |

````json
[
  {
    "id": 1,
    "kind": "unrun-verify",
    "phase": "quick-260817-kkj",
    "file": "assets/images/headshot.jpg",
    "line": null,
    "description": "Task 2's automated <verify> (bundle exec jekyll build) could not run — Ruby/Bundler/Jekyll are not installed in this execution environment. Manual/CI build verification recommended before this lands on the live site.",
    "status": "open",
    "reason": "",
    "recorded_at": "2026-08-17T19:53:55.145Z",
    "resolved_at": null
  }
]
````
