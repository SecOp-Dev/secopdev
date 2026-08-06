# Coding Conventions

**Analysis Date:** 2026-08-05

## Naming Patterns

**Files:**
- `_includes/*.html`: Partial templates for resume sections (e.g., `experiences.html`, `skills.html`, `education.html`)
- `_layouts/*.html`: Main page layouts (e.g., `default.html`, `print.html`, `compress.html`)
- `_sass/*.scss`: Modular stylesheets with underscore prefix (e.g., `_base.scss`, `_tokens.scss`, `_responsive.scss`)
- `assets/js/*.js`: Client-side scripts in camelCase files (e.g., `pdf-generator.js`)
- `_data/*.yml`: YAML data files as source of truth (e.g., `data.yml`)

**Liquid/Template Variables:**
- Use snake_case for Jekyll/Liquid variables: `sidebar`, `experiences`, `skills`, `certifications`
- Loop variables use descriptive names: `experience`, `item`, `category`, `cat`
- Conditional variable assignment with `{% assign %}` for data access: `{% assign sidebar = site.data.data.sidebar %}`

**JavaScript Functions:**
- Use camelCase for function names: `generatePDF()`, `print()`, `syncIcon()`
- Use camelCase for variable names: `printWindow`, `themeToggle`, `nextTheme`
- Use const for immutable variables, standard JavaScript scope rules

**CSS Classes:**
- Use kebab-case for class names: `sidebar-wrapper`, `profile-container`, `skill-tags`, `experiences-section`
- Use BEM-adjacent naming for clarity: `.container-block`, `.container-block-title`, `.job-title`, `.upper-row`
- Follow pattern: `.section-title`, `.action-buttons`, `.action-btn`
- Icon classes use Font Awesome: `.fas`, `.fab`, `.far` combined with specific icon codes

**CSS Variables (Design Tokens):**
- Use double-dash prefix and kebab-case: `--bg`, `--text-strong`, `--accent`, `--font-sans`
- Color tokens: `--bg`, `--panel`, `--panel-alt`, `--border`, `--text`, `--text-strong`, `--text-muted`, `--accent`, `--accent-dim`
- Font tokens: `--font-sans`, `--font-mono`
- Layout tokens: `--shadow`

## Code Style

**Formatting:**
- Jekyll: no linter configured; maintain clean indentation (2 spaces)
- HTML/Liquid: closing tag comments for block clarity (e.g., `<!--//sidebar-wrapper-->`)
- YAML: 2-space indentation; standard YAML structure with nested key-value pairs
- SCSS: 2-space indentation; nested selectors organized by block (comments separate sections)
- JavaScript: 2-space indentation; semicolons at end of statements; wrap code in IIFE when isolated

**Linting:**
- No linter configured (eslint, prettier, or stylelint)
- Maintain consistency with existing style rather than enforcing external rules

## Import Organization

**Liquid Includes (in order):**
1. Data assignment (`{% assign ... = site.data.data.* %}`)
2. Conditional checks (`{% if ... %}`)
3. Content/loop rendering
4. Closing tags

**SCSS Imports:**
Located in `_sass/_default.scss`:
1. `@import "base";` — layout and core styles
2. `@import "responsive";` — media queries and responsive patterns
3. `@import "utilities";` — utility classes
4. `@import "print";` — print-specific styles

**JavaScript:**
Inline scripts in Liquid templates; no module imports. Prefix with IIFE `(function() { ... })()` for scope isolation.

**Jekyll Configuration:**
No path aliases; use Jekyll's standard `site.baseurl` for asset paths

## Error Handling

**Patterns:**
- Liquid: Use conditional checks with `{% if %}` before rendering (e.g., `{% if sidebar.avatar %}`)
- JavaScript: No explicit error handling; rely on browser console for debugging
- YAML: Validated via `bundle exec jekyll serve` (Jekyll will fail if YAML is malformed)
- HTML/Liquid: Use comments to mark section boundaries (`<!--//section-->`), aiding debugging

## Logging

**Framework:** None; uses browser console for JavaScript debugging

**Patterns:**
- No application logging; site is static generated
- Errors caught at build time by Jekyll
- Use print/PDF view (localhost:4000/print) to verify styling and output

## Comments

**When to Comment:**
- Section dividers: `/* ======= Layout ======= */` in SCSS
- Complex conditionals: Explain why a conditional is needed in Liquid templates
- Closing tag markers: `<!--//container-->`
- YAML: Comments for configuration options and feature flags (e.g., `# set to False if you want education in main section`)

**JSDoc/TSDoc:**
- Not used; functions are minimal and self-documenting

## Function Design

**Size:** Keep functions small and focused
- `print()` / `generatePDF()`: Single responsibility (route to print view)
- `syncIcon()`: 3-line utility function
- Theme toggle handler: Isolated in IIFE to avoid global scope pollution

**Parameters:** 
- Avoid excessive parameters; use event objects or data attributes when possible
- Example: theme toggle reads from `data-theme` attribute rather than passing as parameter

**Return Values:** 
- JavaScript functions return void (side effects on DOM/localStorage)
- Liquid templates produce rendered HTML as output

## Module Design

**Exports:**
- No module exports; Jekyll generates static HTML from templates
- Each Liquid include is independently callable and renderered via `{% include %}`

**Barrel Files:**
- No barrel exports; each template/partial is a separate unit
- `_layouts/default.html` acts as the main wrapper, including all partials

## Data Structure Design

**Single Source of Truth:**
- All resume content lives in `_data/data.yml` (per CLAUDE.md conventions)
- Templates read data via `site.data.data.*` accessor pattern
- No duplicate content; changes in data.yml automatically propagate to all templates

**YAML Structure:**
- Top-level sections: `sidebar`, `career-profile`, `highlights`, `education`, `experiences`, `skills`, `certifications`
- Each section contains `title` and `info` (or `list` for skills/certifications)
- Nested objects for complex data (e.g., `sidebar.avatar`, `experience.company`)

**Content Rules (from CLAUDE.md):**
- Never soften metrics; use specific numbers ($5M+, 60%, 22,000+)
- Use industry-standard terminology exactly as spelled (SIEM, SOAR, DFIR, MITRE ATT&CK)
- Bullet points: lead with action + metric, not the tool
- Company context belongs in the `company:` field, not in achievement bullets

---

*Convention analysis: 2026-08-05*
