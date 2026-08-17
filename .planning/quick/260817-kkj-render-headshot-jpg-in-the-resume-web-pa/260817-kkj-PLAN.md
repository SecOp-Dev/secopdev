---
phase: quick-260817-kkj
plan: 01
type: execute
wave: 1
depends_on: []
files_modified:
  - assets/images/headshot.jpg
  - _data/data.yml
autonomous: true
requirements: [render-headshot-jpg]

estimate:
  tokens: 20000
  raw_tokens: 20000
  tasks: 2
  confidence: low

must_haves:
  truths:
    - "The sidebar avatar at the top of the page renders Sean's real headshot, not the placeholder profile.png"
    - "The published image carries no EXIF metadata (camera make/model/timestamp stripped for privacy)"
    - "The image is square-cropped and sized appropriately for the existing 140x140 circular .avatar CSS slot, not stretched or off-center"
  artifacts:
    - assets/images/headshot.jpg
  key_links:
    - "_data/data.yml sidebar.avatar value -> _includes/sidebar.html {{ sidebar.avatar }} -> assets/images/{filename} src path"
</must_haves>
---

<objective>
Swap the placeholder sidebar avatar (`assets/images/profile.png`, actually a generic 800x800 JPEG) for Sean's real headshot, sourced from `/mnt/c/Picz/Headshot.jpg`. The existing `.avatar` CSS (circular crop, responsive clamp sizing, accent border/glow — `_sass/_base.scss` ~line 84) already delivers the "modern, cohesive format" requested; this plan only needs to get a correctly processed real photo into that existing slot.

Purpose: Replace a generic placeholder image with Sean's actual professional photo at the top of the resume page, processed for web (square crop, resized, EXIF-stripped for privacy) and wired through the existing Jekyll data-driven avatar mechanism — no new UI, no CSS changes.
Output: `assets/images/headshot.jpg` (processed, EXIF-stripped, square) + `_data/data.yml` updated to reference it.
</objective>

<execution_context>
@$HOME/.claude/gsd-core/workflows/execute-plan.md
@$HOME/.claude/gsd-core/templates/summary.md
</execution_context>

<context>
@/home/rooter/dev/pro/secopdev-resume/CLAUDE.md
@/home/rooter/dev/pro/secopdev-resume/_data/data.yml
@/home/rooter/dev/pro/secopdev-resume/_includes/sidebar.html

Source photo: `/mnt/c/Picz/Headshot.jpg` (WSL path to Windows `C:\Picz\Headshot.jpg`) — JPEG, 1650x1681px, shot on a Nikon D750, carries EXIF metadata. Do not commit this raw source into the repo — only the processed output.

Existing avatar mechanism (confirmed working, do not redesign):
- `_data/data.yml` line 13: `sidebar.avatar: profile.png` — comment on that line says "place a 100x100 picture inside /assets/images/ folder and provide the name of the file below".
- `_includes/sidebar.html` lines 4-12: renders `<img class="avatar" src="{{ site.baseurl }}/assets/images/{{ sidebar.avatar }}" width="140" height="140" alt="{{ sidebar.name }}" loading="eager">` as the first block inside `.profile-container`, above the name/tagline.
- `_sass/_base.scss` `.avatar` (~line 84): `clamp(96px, 14vw, 140px)` responsive square, `aspect-ratio: 1/1`, `object-fit: cover`, `border-radius: 50%` (circular crop happens in CSS, so the source image itself only needs to be square/centered, not pre-circled).
- Environment: Python 3.12 with Pillow installed. No ImageMagick binary available — use Pillow only.
</context>

<tasks>

<task type="tracer">
  <name>Task 1: Process headshot and wire it through the existing avatar slot end-to-end</name>
  <files>assets/images/headshot.jpg, _data/data.yml</files>
  <action>
    Write and run a one-off Python/Pillow script (scratch script, not committed) that:
    1. Opens `/mnt/c/Picz/Headshot.jpg`.
    2. Applies `PIL.ImageOps.exif_transpose()` first to bake in correct orientation from EXIF before the EXIF data is discarded (source is 1650x1681, near-square but not exact — skipping this risks a sideways-rotated final image if the camera wrote an orientation tag).
    3. Center-crops to a square (1:1 aspect ratio) using the shorter dimension, keeping the face/head centered in frame — visually inspect the source dimensions (1650x1681) and crop symmetrically from the longer axis.
    4. Resizes the square crop down to 500x500px using `Image.LANCZOS` resampling — large enough to stay sharp at the CSS clamp's max 140px on retina/high-DPI displays (140px * ~3x for safety margin), small enough to keep file size reasonable for a resume site.
    5. Saves as JPEG quality=85 to `assets/images/headshot.jpg` in the repo, using `img.save(..., exif=b"")` (or simply re-saving through a fresh `Image.new`/`.convert("RGB")` copy that never carries the source `.info["exif"]` forward) so no EXIF block is written to the output — do not pass the source image's `.info` dict through.
    Update `_data/data.yml` line 13: change `avatar: profile.png` to `avatar: headshot.jpg`, keeping the existing inline comment.
    Do not modify `_includes/sidebar.html`, `_sass/_base.scss`, `_sass/_responsive.scss`, or `_layouts/default.html` — the existing avatar rendering and styling already apply correctly to any filename referenced by `sidebar.avatar`.
    Leave the old `assets/images/profile.png` file in place (unused, not referenced) — do not delete it, in case it's referenced elsewhere or wanted as a fallback; removing dead assets is out of scope for this task.
  </action>
  <verify>
    <automated>ruby -e "require 'yaml'; d = YAML.load_file('_data/data.yml'); raise 'avatar not updated' unless d['sidebar']['avatar'] == 'headshot.jpg'; puts 'OK'"</automated>
  </verify>
  <done>
    `assets/images/headshot.jpg` exists, is a square JPEG (verify via `python3 -c "from PIL import Image; im=Image.open('assets/images/headshot.jpg'); assert im.size[0]==im.size[1]; assert not im.getexif(); print(im.size)"` — width equals height and EXIF dict is empty), and `_data/data.yml`'s `sidebar.avatar` value is `headshot.jpg`.
  </done>
</task>

<task type="auto">
  <name>Task 2: Verify the rendered page and confirm no build regressions</name>
  <files>none (verification only)</files>
  <action>
    Run the project's standard build check from CLAUDE.md: start `bundle exec jekyll serve` (or `bundle exec jekyll build` if a quick non-serving check is preferred) and confirm the site builds without errors. Confirm the generated `_site/assets/images/headshot.jpg` (or `_site` output) exists after build. If a browser/screenshot check is available in this environment, load the local site and visually confirm the sidebar avatar shows the real headshot (circular, centered, not stretched) instead of the old placeholder — otherwise rely on the build success plus the file-existence/YAML checks from Task 1 as sufficient evidence for this quick task.
  </action>
  <verify>
    <automated>bundle exec jekyll build 2>&1 | tail -20 && test -f _site/assets/images/headshot.jpg && echo BUILD_OK</automated>
  </verify>
  <done>Jekyll build completes with no errors and `_site/assets/images/headshot.jpg` exists in the built output, confirming the new avatar is correctly served.</done>
</task>

</tasks>

<threat_model>
## Trust Boundaries

| Boundary | Description |
|----------|-------------|
| Source image (Windows filesystem) -> repo asset | Raw photo file from outside the repo crosses into a publicly-hosted git repo and website (career.secop.dev) |

## STRIDE Threat Register

| Threat ID | Category | Component | Severity | Disposition | Mitigation Plan |
|-----------|----------|-----------|----------|-------------|-----------------|
| T-quick-260817-01 | Information Disclosure | assets/images/headshot.jpg | medium | mitigate | Strip EXIF metadata (camera make/model, GPS if present, capture timestamp) via Pillow re-save without carrying forward `.info["exif"]`, per Task 1 step 5, before the file is ever committed or published to the public site |
| T-quick-260817-02 | Information Disclosure | raw source file `/mnt/c/Picz/Headshot.jpg` | low | accept | Source file lives outside the repo (Windows filesystem, WSL-mounted) and is never copied or committed directly — only the processed, EXIF-stripped output is written into the repo |
</threat_model>

<verification>
- `_data/data.yml` parses as valid YAML and `sidebar.avatar` equals `headshot.jpg`.
- `assets/images/headshot.jpg` exists, is square, and has no EXIF data.
- `bundle exec jekyll build` completes without errors and produces `_site/assets/images/headshot.jpg`.
</verification>

<success_criteria>
The sidebar at the top of the resume page renders Sean's real headshot (not the generic placeholder), using the existing circular/responsive `.avatar` styling, with EXIF metadata stripped from the published image file, and the Jekyll build remains green.
</success_criteria>

<output>
Create `.planning/quick/260817-kkj-render-headshot-jpg-in-the-resume-web-pa/260817-01-SUMMARY.md` when done
</output>
