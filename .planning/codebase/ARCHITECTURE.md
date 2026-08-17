<!-- refreshed: 2026-08-05 -->
# Architecture

**Analysis Date:** 2026-08-05

## System Overview

```text
┌─────────────────────────────────────────────────────────────┐
│                    Entry Points (Web)                        │
│  `index.html` (Web) / `print.html` (Print)                  │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              Layout Templates (Liquid)                       │
│  `_layouts/default.html` or `_layouts/print.html`           │
│  - Head (meta, fonts, styles, structured data)              │
│  - Sidebar + Main wrapper                                   │
│  - Footer & Scripts                                         │
└─────────┬──────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────┐
│           Template Components (_includes/)                  │
│  - sidebar.html (profile, contact, education, interests)   │
│  - career-profile.html, highlights.html                    │
│  - experiences.html, skills.html, certifications.html      │
│  - recommendations.html, projects.html                      │
│  - contact.html, footer.html, scripts.html                 │
└─────────┬──────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────┐
│         Data Layer (YAML)                                    │
│         `_data/data.yml`                                     │
│  - sidebar (name, contact, links, interests)                │
│  - career-profile, highlights, experiences, education      │
│  - certifications, projects, skills, recommendations       │
└─────────┬──────────────────────────────────────────────────┘
          │
          ▼
┌─────────────────────────────────────────────────────────────┐
│         Styling Layer (SCSS → CSS)                           │
│  `assets/css/main.scss` → `assets/css/main.css`             │
│  - Design tokens (dark/light themes)                        │
│  - Base styles, responsive layout, print styles            │
│  - Bootstrap framework, Font Awesome icons                  │
└─────────────────────────────────────────────────────────────┘
```

## Component Responsibilities

| Component | Responsibility | File |
|-----------|----------------|------|
| **index.html** | Main web entry point; includes all resume sections | `index.html` |
| **print.html** | Print-optimized entry point; links to print layout | `print.html` |
| **default.html** | Standard web layout; sidebar + main content wrapper | `_layouts/default.html` |
| **print.html (layout)** | Print-optimized layout; full-width styling | `_layouts/print.html` |
| **head.html** | Meta tags, fonts, styles, structured data (JSON-LD) | `_includes/head.html` |
| **sidebar.html** | Profile container, contact info, conditional sections | `_includes/sidebar.html` |
| **main content sections** | Career profile, experiences, skills, recommendations | `_includes/*.html` |
| **scripts.html** | Theme toggle, localStorage persistence | `_includes/scripts.html` |
| **footer.html** | Site footer with attribution | `_includes/footer.html` |
| **main.scss** | Entry point for SCSS compilation; defines sidebar order | `assets/css/main.scss` |
| **_tokens.scss** | CSS custom properties (dark/light theme colors) | `_sass/_tokens.scss` |
| **_base.scss** | Base typography, spacing, layout structure | `_sass/_base.scss` |
| **_default.scss** | Component styles (sections, items, sidebar, buttons) | `_sass/_default.scss` |
| **_responsive.scss** | Media queries for mobile/tablet/desktop | `_sass/_responsive.scss` |
| **_print.scss** | Print-specific style overrides | `_sass/_print.scss` |
| **data.yml** | Single source of truth for all resume content | `_data/data.yml` |
| **_config.yml** | Jekyll build settings, site metadata, SASS config | `_config.yml` |

## Pattern Overview

**Overall:** Static Site Generation (SSG) with data-driven templating

**Key Characteristics:**
- Jekyll-powered static site generator for predictable builds
- YAML data source (`_data/data.yml`) is the single source of truth for resume content
- Liquid templating language for conditional rendering and iteration
- Component-based architecture via `_includes/` for reusable sections
- CSS design token system for theme switching (dark/light modes)
- Print and web paths diverge at layout level (`_layouts/default.html` vs `_layouts/print.html`)
- Browser-side theme persistence via localStorage and CSS custom properties
- No JavaScript frameworks; vanilla JS for lightweight interaction

## Layers

**Configuration Layer:**
- Purpose: Site-wide settings, Jekyll build parameters
- Location: `_config.yml`
- Contains: Site title, URL, baseurl, SASS config, compression settings, build exclusions
- Depends on: None
- Used by: Jekyll build process

**Data Layer:**
- Purpose: Single source of truth for all resume content
- Location: `_data/data.yml`
- Contains: Sidebar info (name, contact, links), career profile, experiences, education, certifications, skills, projects, recommendations, highlights
- Depends on: None (static YAML)
- Used by: All templates via `site.data.data.<section>`

**Layout Layer:**
- Purpose: Structural HTML templates for page rendering
- Location: `_layouts/default.html`, `_layouts/print.html`, `_layouts/compress.html`
- Contains: HTML boilerplate, head includes, sidebar/main wrapper, footer/script includes
- Depends on: Liquid syntax, `_includes/head.html`, `_includes/footer.html`, `_includes/scripts.html`
- Used by: Page files (`index.html`, `print.html`)

**Component Layer:**
- Purpose: Reusable template partials for individual resume sections
- Location: `_includes/` directory
- Contains: Profile sidebar components, section renderers (experiences, skills, education, etc.)
- Depends on: Data layer (`site.data.data`), Liquid filters (markdownify)
- Used by: Page and layout files via `{% include %}`

**Styling Layer:**
- Purpose: SCSS→CSS compilation with design tokens and responsive design
- Location: `assets/css/main.scss`, `_sass/` directory
- Contains: CSS custom properties (colors, fonts, shadows), component styles, responsive breakpoints, print overrides
- Depends on: Bootstrap framework, Font Awesome icons (linked in head)
- Used by: Browser rendering of `assets/css/main.css`

**Asset Layer:**
- Purpose: External dependencies and static resources
- Location: `assets/plugins/` (Bootstrap, Font Awesome), `assets/images/`, `assets/js/`
- Contains: Third-party CSS/JS libraries, images (avatar), client-side JavaScript
- Depends on: None
- Used by: Head includes and default layout

## Data Flow

### Primary Request Path (Web)

1. User visits `https://career.secop.dev/` → browser requests `index.html` (`index.html`)
2. Jekyll renders `index.html` using `layout: default` → loads `_layouts/default.html` (`_layouts/default.html`)
3. Layout includes `head.html` → loads meta tags, fonts (Google Fonts: Inter, JetBrains Mono), Bootstrap CSS, Font Awesome, `main.css` (`_includes/head.html`)
4. Layout includes sidebar and main wrapper (`_layouts/default.html:25-29`)
5. `index.html` includes sections in sequence: career-profile, highlights, education (if not in sidebar), experiences, certifications, projects, recommendations, skills (`index.html:6-26`)
6. Each section include (e.g., `_includes/experiences.html`) iterates over data: `{% for experience in site.data.data.experiences.info %}` and renders items
7. All data comes from `_data/data.yml` via Liquid's `site.data.data.<section>` accessors
8. SCSS compiles from `assets/css/main.scss` → imports `_tokens.scss` (theme tokens), imports `_default.scss` (all component styles)
9. Theme token is read from localStorage (set by `_includes/scripts.html`) and applied as `data-theme="dark"` or `data-theme="light"` on `<html>` element
10. CSS custom properties in `:root` and `[data-theme="light"]` selectors override colors based on theme
11. Bootstrap grid and Font Awesome icons provide layout and iconography

**State Management:**
- Theme state: stored in localStorage, persisted across page reloads via inline script in `_includes/head.html` line 3
- Sidebar visibility/position: conditional rendering via `site.data.data.sidebar.*` flags (e.g., `sidebar.education`, `sidebar.position`)
- Print state: separate URL path `/print` routes to `print.html` → `_layouts/print.html` (no sidebar, full-width, print styles applied)

### Print/PDF Path

1. User clicks "PDF" button (or Print button) → `assets/js/pdf-generator.js` opens `/print` in new window
2. Browser requests `/print` → Jekyll renders `print.html` with `layout: print` (`print.html`)
3. `_layouts/print.html` applies light theme override (`data-theme="light"`) and print CSS overrides (`_sass/_print.scss`)
4. Print layout includes same components as default, but with print-optimized styles (no action buttons, full width, optimized spacing)
5. Browser's print dialog appears; user can print to PDF or physical printer
6. Print styles from `_sass/_print.scss` hide UI elements (buttons, theme toggle) and optimize layout for paper

### Theme Toggle Flow

1. User clicks theme-toggle button (`_layouts/default.html:13-14`)
2. `_includes/scripts.html` event listener fires; toggles `data-theme` from "dark" to "light" or vice versa (line 11)
3. New theme is persisted to localStorage (line 13)
4. CSS cascade picks up new theme's custom properties from `[data-theme="light"]` selector in `_sass/_tokens.scss`
5. All colors update instantly (browser reflow, no page reload needed)

## Key Abstractions

**Data Sections:**
- Purpose: Logical groupings of resume content that map 1:1 to template sections
- Examples: `sidebar`, `experiences`, `skills`, `recommendations`, `certifications`, `projects`
- Pattern: YAML object with `title` + `info` (array) or `title` + `categories`/`list`; rendered by corresponding `_includes/*.html`

**Template Includes:**
- Purpose: Reusable, section-specific HTML renderers
- Examples: `_includes/experiences.html` loops `site.data.data.experiences.info` and renders each as `.item`
- Pattern: Conditional wrapper + loop → item markup + styling classes; wraps content with Font Awesome icon + section title

**CSS Custom Properties (Design Tokens):**
- Purpose: Single source of truth for colors, fonts, shadows across both themes
- Examples: `--bg`, `--text`, `--accent`, `--shadow` defined in `:root` (dark) and `[data-theme="light"]`
- Pattern: Property defined in both theme blocks with corresponding light/dark values; used throughout component SCSS

**Sidebar Toggles:**
- Purpose: Declarative control over which sections appear in sidebar vs. main area
- Examples: `sidebar.education: True` moves education to sidebar; `sidebar.position: right` sets sidebar order
- Pattern: Boolean/string flags in `_data/data.yml` control `{% if %}` blocks in templates

## Entry Points

**Web Entry Point:**
- Location: `index.html`
- Triggers: User visits domain (https://career.secop.dev/)
- Responsibilities: Includes all resume sections in order; uses default web layout; renders with theme from localStorage

**Print Entry Point:**
- Location: `print.html` (permalink `/print`)
- Triggers: User clicks "PDF" or "Print" button; manually navigates to `/print`
- Responsibilities: Same sections as web, but with print layout; forces light theme; optimizes for paper/PDF export

**Layout Entry Points:**
- `_layouts/default.html`: Used by `index.html`; renders sidebar + main; includes action buttons (theme toggle, print, PDF)
- `_layouts/print.html`: Used by `print.html`; same structure but with print-specific styles; forces light theme

**Component Entry Points:**
- `_includes/head.html`: Included by all layouts; loads meta, fonts, styles, structured data
- `_includes/sidebar.html`: Included by default layout; renders profile, contact, optional education/interests
- Section includes (e.g., `_includes/experiences.html`): Included by page files; render individual sections

## Architectural Constraints

- **Client-Side Rendering:** Theme toggle is entirely client-side (localStorage + CSS custom properties). Server sends static HTML; browser applies theme after script runs.
- **Single Data Source:** All content lives in `_data/data.yml`. No API calls, no dynamic backend; Jekyll build is the sole content synchronization point.
- **No Dynamic Sections:** Sections shown/hidden via conditional Liquid (`{% if %}`), not JavaScript. If a section's data is empty, the entire section is omitted from HTML.
- **Print Layout Divergence:** Print and web paths require separate layout files because print-optimized styles (no buttons, full width) conflict with web styles. Both share components but diverge at layout level.
- **Static Generation:** Site is fully static; no server-side processing. All URLs are file-based or use Jekyll's `permalink` front matter.
- **Sidebar Order:** Controlled by `sidebar.position` flag in YAML. Main order is fixed by Flexbox `order` property set via SCSS variable (`$sidebar-order`, `$resume-order`) computed in `main.scss` front matter.
- **Theme Persistence:** Relies on browser localStorage. Anonymous browsing or disabled storage means theme defaults to "dark" (set in head.html inline script).
- **Google Fonts Loading:** Synchronous (blocks rendering until fonts load). Inter and JetBrains Mono are preconnected but not font-display optimized for performance.

## Anti-Patterns

### Over-Reliance on Data Flags for UI Control

**What happens:** Template logic checks `sidebar.education`, `sidebar.about`, etc. to hide/show sections. Adding a new conditional section requires editing both YAML and template.

**Why it's wrong:** Separates presentation intent from component definition. A future developer won't know which YAML keys trigger template conditionals unless they grep the includes.

**Do this instead:** Document all conditional flags in `_data/data.yml` with comments explaining their effect (e.g., `# Set to False to move education to main section`). Keep all conditionals in `_includes/` where they're visible.

### Unvalidated Markdown in YAML

**What happens:** `details` fields in experiences/projects contain inline HTML and markdown (e.g., `<br />`, `•` bullets). Rendered via `| markdownify` filter. If user enters malicious HTML, it passes through Jekyll's markdown processor.

**Why it's wrong:** User-generated content in YAML can introduce XSS if not sanitized. The site is single-author (resume), but if content is ever automated or imported, HTML/markdown injection becomes a risk.

**Do this instead:** Restrict YAML fields to plain text or safe markdown. If HTML is needed, pre-render it in a safe context (e.g., separate HTML partial) or use a markdown plugin with HTML stripping enabled.

### Print Layout Overrides in Inline Styles

**What happens:** `_layouts/print.html` includes inline `<style>` tags to override `.wrapper` padding and max-width. This duplicates styling logic already in `_sass/_print.scss`.

**Why it's wrong:** Inline styles have higher specificity than external CSS, making it hard to debug and override. Maintenance burden increases when print styling logic is spread across multiple files.

**Do this instead:** Move all print overrides into `_sass/_print.scss` with media query `@media print`. Use Jekyll's `compress` layout to minify if needed, but keep all SCSS centralized.

### Hardcoded Social Links in Head Metadata

**What happens:** `_includes/head.html` hardcodes LinkedIn/GitHub URLs in schema.org markup (lines 68-69) using `site.data.data.sidebar.linkedin` and `.github`. If these keys are missing or renamed, structured data breaks.

**Why it's wrong:** No fallback; if a user deletes a key from YAML, the page's structured data silently fails validation (schema.org SEO impact).

**Do this instead:** Add `{% if %}` guards around social links in schema.org markup. Log a warning in build output if keys are missing (e.g., Jekyll plugin or post-build script).

## Error Handling

**Strategy:** Defensive templating with conditional rendering

**Patterns:**
- All section includes start with `{% if <section> %}` (e.g., line 2 of `_includes/experiences.html`). If data is missing, section is omitted silently.
- Contact fields in `_includes/contact.html` are conditionally rendered: only display if the corresponding value in YAML is present (e.g., `{% if sidebar.email %}`).
- Education placement is conditional: if `sidebar.education: True`, education appears in sidebar include chain; if `False`, education is included in main page (see `index.html` lines 10-12).
- Theme toggle gracefully degrades: if localStorage is unavailable or disabled, theme defaults to "dark" (hardcoded in head.html inline script).
- PDF generation falls back to browser's native print: if external PDF library is unavailable, `print()` JS function still works (uses browser print dialog).

**No explicit error logging or exception handling:**
- Build errors: Jekyll logs to console during build; missing files or Liquid syntax errors cause build failure.
- Runtime errors: No try/catch in JavaScript; if localStorage fails, page continues (theme just reverts to default on reload).
- Template errors: Liquid syntax errors cause Jekyll build failure; data validation is implicit (missing keys = section omitted).

## Cross-Cutting Concerns

**Logging:** None. Static site generation means no runtime logs. Build-time errors are caught by Jekyll.

**Validation:** Implicit. If YAML is malformed, Jekyll build fails. No schema validation (e.g., JSON Schema) for `_data/data.yml`.

**Authentication:** None. Site is public; no auth required. Deployment uses GitHub Pages (no access control).

**Accessibility:**
- Semantic HTML: Sections use `<section>`, headings use `<h2>`/`<h3>`, lists use `<ul>/<li>` in skills
- ARIA: Limited. Theme toggle button has `aria-label="Toggle theme"` (line 13 of default.html). Icon hiding uses `aria-hidden="true"` in section titles.
- Contrast: Dark theme uses high-contrast colors (light text on dark bg). Light theme uses dark text on light bg. Accent green meets WCAG AA.
- Print: Print stylesheet hides interactive elements; print output is text-only and screen-reader friendly.

---

*Architecture analysis: 2026-08-05*
