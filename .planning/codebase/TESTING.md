# Testing Patterns

**Analysis Date:** 2026-08-05

## Test Framework

**Status:** No test framework configured

This is a Jekyll static site generator project. No testing frameworks are installed (no rspec, minitest, jest, vitest, etc.). The Gemfile contains only:
- `jekyll`
- `github-pages`
- `webrick`

**Validation Approach:**
Testing relies on:
1. Jekyll build process (catches YAML errors, Liquid syntax errors)
2. Manual visual verification at `localhost:4000`
3. Link checking and HTML validation (optional)

## Test File Organization

**No test files exist** in this codebase. All testing is implicit:
- YAML syntax validation via Jekyll parsing
- Liquid template rendering verification
- Browser-based visual verification

## Manual Validation Commands

**Build & Serve (via Docker):**
```bash
docker-compose up
# Site available at http://localhost:4000
```

**Quick YAML Validation (without Jekyll):**
```bash
ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"
```

**Build without serving:**
```bash
bundle exec jekyll build
# Outputs to _site/ directory
```

## Content Validation

**What to Check Manually:**

1. **YAML Structure Integrity:**
   - Correct nesting and indentation in `_data/data.yml`
   - All required top-level sections present (sidebar, career-profile, experiences, skills, education)
   - No trailing colons or malformed YAML

2. **Data Completeness:**
   - Verify all fields mentioned in CLAUDE.md content rules are populated:
     - Career profile: specific metrics (not vague language)
     - Experience bullets: action + metric first, then tool/approach
     - Skills: using `categories` array structure (not old `toolset` progress bars)
     - Company context in `company:` field, not in achievement bullets

3. **Template Rendering:**
   - After changes to `_data/data.yml`, serve locally and verify:
     - All sections render without Liquid errors
     - No empty sections (verify conditional rendering works)
     - Proper HTML structure with no missing closing tags
     - Correct styling applies (including dark/light theme toggle)

4. **Link Verification:**
   - Sidebar contact links: email, LinkedIn, GitHub, website
   - All href attributes resolve or link to external sites
   - Favicon loads without 404 errors

5. **Print/PDF Output:**
   - Use browser print-to-PDF at `/print` route
   - Verify text is ATS-parseable (no image-only content)
   - Check page breaks and formatting for multiple pages

## Mocking

**Not applicable.** This is a static site generator with no API calls, external dependencies, or stateful behavior to mock. All content is pre-computed at build time from `_data/data.yml`.

## Fixtures and Factories

**Not applicable.** Data is defined once in `_data/data.yml`; no test data fixtures are needed.

## Coverage

**Requirements:** No coverage tracking (static site, no code coverage tooling)

**Implicit Coverage:**
- Every template file is rendered on every build
- Every Liquid conditional is tested by the build process (syntax errors cause failure)
- Visual coverage is verified manually at localhost:4000

## Test Types

**No formal test suite exists.** Instead:

### Build-Time Validation (Implicit)
- **What happens:** `bundle exec jekyll serve` or `bundle exec jekyll build` runs
- **Catches:** YAML syntax errors, Liquid template errors, missing includes, undefined Liquid variables
- **Scope:** Entire site generation pipeline

### Manual Visual Verification (Recommended)
- **Scope:** Design consistency, content rendering, responsive layout
- **Approach:** Serve locally, inspect each section, test dark/light theme toggle, check print view
- **Files to inspect:** All of `_includes/`, `_layouts/`, and `_sass/`

### Link Checking (Optional)
- **Tools:** HTMLProofer gem (not currently installed, but can be added to Gemfile)
- **Scope:** Validate all href/src attributes point to valid resources
- **Setup:** Add `gem 'html-proofer'` to Gemfile, then run: `bundle exec htmlproofer ./_site`

## CI/CD Testing

**GitHub Pages Deployment:**
No automated test suite in CI. GitHub Pages builds and serves on push to `main` branch.

**Recommended additions:**
- Add `html-proofer` gem and run link checks in CI workflow
- Add YAML schema validation step (e.g., with `yamllint`)
- Add a build-time step to verify no Liquid errors: `bundle exec jekyll build --strict_front_matter`

## Debugging Strategy

**If Jekyll build fails:**
1. Check error output for YAML line number
2. Verify indentation in `_data/data.yml` (2 spaces, not tabs)
3. Run the quick YAML validation command: `ruby -e "require 'yaml'; YAML.load_file('_data/data.yml'); puts 'OK'"`
4. Check for common Liquid errors (unclosed `{% if %}`, undefined variables, typos in `site.data.data.*` paths)

**If content doesn't render:**
1. Verify the section is enabled in `_data/data.yml` (e.g., `education: True`, `sidebar.about: False`)
2. Check that the corresponding include file exists in `_includes/` (e.g., `_includes/education.html`)
3. Verify the main template `_layouts/default.html` or `index.html` includes the partial
4. Check browser console for JavaScript errors (open DevTools at localhost:4000)

**If styling is broken:**
1. Verify CSS compiles from SCSS: check `assets/css/main.css` is generated
2. Check dark/light theme toggle works (localStorage should store preference)
3. Inspect element in browser to verify class names match SCSS selectors
4. Verify design tokens in `_sass/_tokens.scss` are syntactically correct

---

*Testing analysis: 2026-08-05*
