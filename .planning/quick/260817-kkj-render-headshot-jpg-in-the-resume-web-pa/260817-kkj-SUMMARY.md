---
phase: quick-260817-kkj
plan: 01
subsystem: ui
tags: [jekyll, pillow, image-processing, avatar]

# Dependency graph
requires: []
provides:
  - "assets/images/headshot.jpg — real, EXIF-stripped, square-cropped headshot in the existing 500x500 source, served through the 140x140 circular .avatar CSS slot"
  - "_data/data.yml sidebar.avatar wired to headshot.jpg"
affects: []

actuals:
  tokens: 250
  tasks: 2
  commits: 1

tech-stack:
  added: []
  patterns:
    - "Image processing done via a scratch (uncommitted) Pillow script outside the repo, writing only the processed output into the repo — raw personal source photo never enters git history"

key-files:
  created:
    - assets/images/headshot.jpg
  modified:
    - _data/data.yml

key-decisions:
  - "Resized to 500x500 (not the 100x100 the data.yml comment suggests) to stay sharp on retina displays at the CSS clamp's 140px max, per plan's explicit sizing rationale"
  - "Used exif_transpose() before crop to bake in camera orientation, then discarded EXIF entirely on save (fresh Image.new copy, never carrying forward .info['exif'])"
  - "Left old assets/images/profile.png in place, unreferenced — out of scope to delete per plan"

patterns-established: []

requirements-completed: [render-headshot-jpg]

coverage:
  - id: D1
    description: "Sidebar avatar renders Sean's real headshot instead of the placeholder profile.png"
    requirement: "render-headshot-jpg"
    verification:
      - kind: other
        ref: "python3 -c \"from PIL import Image; im=Image.open('assets/images/headshot.jpg'); assert im.size[0]==im.size[1]; assert not im.getexif()\" -> (500, 500), exif {}"
        status: pass
      - kind: other
        ref: "python3 yaml.safe_load('_data/data.yml')['sidebar']['avatar'] == 'headshot.jpg' -> OK (ruby unavailable in this environment, substituted equivalent python3+pyyaml check for the plan's ruby -e verify command)"
        status: pass
    human_judgment: false
  - id: D2
    description: "Jekyll site builds cleanly with the new avatar and the built _site output includes the headshot"
    requirement: "render-headshot-jpg"
    verification: []
    human_judgment: true
    rationale: "Ruby/Bundler/Jekyll are not installed in this execution environment, so `bundle exec jekyll build` (Task 2's automated verify) could not be run. Recorded as an unrun-verify entry in .planning/WINDOWS.md. A human or CI environment with the project's Ruby toolchain must run `bundle exec jekyll build` (or `jekyll serve` and visually check the sidebar) before this is considered fully verified."

duration: 5min
completed: 2026-08-17
status: complete
---

# Quick Task 260817-kkj: Render Headshot JPG in Resume Sidebar Summary

**Replaced the generic placeholder sidebar avatar with Sean's real headshot — center-cropped square, resized to 500x500, EXIF metadata stripped — wired through the existing `sidebar.avatar` Jekyll data field with zero template/CSS changes.**

## Performance

- **Duration:** 5 min
- **Started:** 2026-08-17T19:49:00Z
- **Completed:** 2026-08-17T19:54:02Z
- **Tasks:** 2 (1 committed, 1 verification-only)
- **Files modified:** 2

## Accomplishments
- Processed `/mnt/c/Picz/Headshot.jpg` (1650x1681, Nikon D750, carrying EXIF) into `assets/images/headshot.jpg`: EXIF-orientation baked in via `exif_transpose()`, center-cropped to a 1650x1650 square, resized to 500x500 with LANCZOS resampling, saved through a fresh `Image.new` copy so no `.info["exif"]` block survives into the output.
- Updated `_data/data.yml` `sidebar.avatar: profile.png` -> `sidebar.avatar: headshot.jpg`, preserving the existing inline comment — no changes to `_includes/sidebar.html` or any `_sass/*` file, matching the plan's "existing mechanism, do not redesign" instruction.
- Left `assets/images/profile.png` untouched and unreferenced, as instructed (out of scope to delete).

## Task Commits

Each task was committed atomically:

1. **Task 1: Process headshot and wire it through the existing avatar slot end-to-end** - `b321f3a` (feat)
2. **Task 2: Verify the rendered page and confirm no build regressions** - no commit (verification-only task, no files modified; see Deviations for why the literal automated verify command could not run)

## Files Created/Modified
- `assets/images/headshot.jpg` - New processed headshot (500x500 JPEG, quality 85, no EXIF), 41,183 bytes
- `_data/data.yml` - `sidebar.avatar` value changed from `profile.png` to `headshot.jpg`

## Decisions Made
- Sized the output at 500x500 rather than the 100x100 the data.yml comment literally suggests, per the plan's own stated rationale (retina sharpness at the CSS clamp's 140px max, ~3x safety margin).
- Stripped EXIF by constructing a fresh `Image.new` + `.paste()` copy rather than passing `exif=b""` to `.save()`, to guarantee no `.info["exif"]` dict survives regardless of Pillow's save-path EXIF handling.

## Deviations from Plan

### Auto-fixed Issues

None - plan's Task 1 executed exactly as written.

### Environment Limitations (not auto-fixable, documented per Rule 3 exclusion reasoning)

**1. Ruby/Jekyll toolchain unavailable in this execution environment**
- **Found during:** Task 1's automated verify command and all of Task 2
- **Issue:** The plan's Task 1 verify (`ruby -e "require 'yaml'; ..."`) and Task 2's entire action/verify (`bundle exec jekyll build`) require Ruby, Bundler, and Jekyll — none of which are installed on this machine (`which ruby bundle gem` all resolve to nothing).
- **Handling:** Substituted an equivalent Python + PyYAML check for Task 1's verify (confirmed `sidebar.avatar == 'headshot.jpg'`, same semantic result). Task 2's `bundle exec jekyll build` could not be substituted equivalently — no Python/Jekyll-compatible build tool was reached for. Did not attempt to install a full Ruby/Jekyll toolchain, which would be a significant, out-of-scope environmental change for a quick image-swap task (and is exactly the kind of package/toolchain install this workflow's deviation rules treat cautiously).
- **Verification performed instead:** Confirmed `_includes/sidebar.html` (unmodified) already correctly interpolates `{{ sidebar.avatar }}` into the `<img src>` path; confirmed the new file is a valid 500x500 JPEG with no EXIF; confirmed `_data/data.yml` still parses as valid YAML.
- **Tracked:** Recorded as an `unrun-verify` entry in `.planning/WINDOWS.md` (id 1) — a human or CI run with the project's Ruby toolchain should run `bundle exec jekyll build` (or `jekyll serve` + visual check) before this change is considered fully verified end-to-end.
- **Files modified:** None beyond Task 1's.
- **Committed in:** N/A (verification-only gap, no code change needed)

---

**Total deviations:** 0 auto-fixed. 1 environment limitation documented (build-tool verification deferred to a Ruby-capable environment).
**Impact on plan:** Core deliverable (real headshot wired through existing avatar slot, EXIF-stripped, square) is fully complete and verified by every check available in this environment. Only the final Jekyll-build confirmation is deferred.

## Issues Encountered
- `ruby` was not found on PATH in this execution environment, so Task 1's literal `ruby -e ...` verify command could not run as written — substituted an equivalent Python/PyYAML check with identical pass/fail semantics (see Deviations above).
- `bundle` was not found either, so Task 2's `bundle exec jekyll build` verify could not run at all — see Deviations above for what was verified instead and where the gap is tracked.

## User Setup Required

None - no external service configuration required. Recommended (not required) follow-up: run `bundle exec jekyll build` (or `jekyll serve` and load `localhost:4000`) in an environment with the project's Ruby toolchain to visually confirm the sidebar avatar renders correctly and the build stays green, then close out the `unrun-verify` entry in `.planning/WINDOWS.md`.

## Next Phase Readiness
- Headshot is live in the working tree and committed; `career.secop.dev`'s next deploy will pick it up once this branch merges/publishes.
- Old `assets/images/profile.png` remains in the repo, unreferenced — a future cleanup task could remove it, but that's explicitly out of scope here.

---
*Phase: quick-260817-kkj*
*Completed: 2026-08-17*

## Self-Check: PASSED

- FOUND: assets/images/headshot.jpg
- FOUND: .planning/quick/260817-kkj-render-headshot-jpg-in-the-resume-web-pa/260817-kkj-SUMMARY.md
- FOUND: b321f3a (git log --oneline --all)
