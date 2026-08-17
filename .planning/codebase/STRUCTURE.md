# Codebase Structure

**Analysis Date:** 2026-08-05

## Directory Layout

```
secopdev-resume/
├── _config.yml               # Jekyll build configuration & site metadata
├── _data/
│   └── data.yml              # Single source of truth for all resume content
├── _layouts/
│   ├── default.html          # Main layout: sidebar + content + buttons
│   ├── print.html            # Print-optimized layout: light theme, no buttons
│   └── compress.html         # Compressed output layout (unused)
├── _includes/
│   ├── head.html             # Meta tags, fonts, styles, structured data
│   ├── sidebar.html          # Profile container + contact + conditionals
│   ├── career-profile.html   # Career summary section
│   ├── highlights.html       # Career highlights (bullet list)
│   ├── experiences.html      # Work experience entries
│   ├── education.html        # Education entries
│   ├── certifications.html   # Certifications & training
│   ├── projects.html         # Projects/assignments
│   ├── skills.html           # Technical skills by category
│   ├── recommendations.html  # Recommendations/testimonials
│   ├── contact.html          # Contact info (sidebar)
│   ├── interests.html        # Personal interests (sidebar)
│   ├── language.html         # Language section (sidebar)
│   ├── about.html            # About section (sidebar, optional)
│   ├── oss-contributions.html # OSS contributions section
│   ├── publications.html     # Publications section
│   ├── footer.html           # Site footer
│   └── scripts.html          # Theme toggle JS
├── _sass/
│   ├── _tokens.scss          # CSS custom properties (dark/light themes)
│   ├── _base.scss            # Base typography, layout structure
│   ├── _default.scss         # Component styles (all sections)
│   ├── _responsive.scss      # Media queries for mobile/tablet/desktop
│   ├── _utilities.scss       # Utility classes
│   └── _print.scss           # Print-specific overrides
├── assets/
│   ├── css/
│   │   └── main.scss         # SCSS entry point (compiles to main.css)
│   ├── js/
│   │   └── pdf-generator.js  # PDF/print trigger JS
│   ├── images/
│   │   └── profile.png       # Avatar image
│   └── plugins/
│       ├── bootstrap/        # Bootstrap CSS framework
│       └── font-awesome/     # Font Awesome icon library
├── index.html                # Main web entry point
├── print.html                # Print/PDF entry point
├── robots.txt                # SEO robots directives
├── sitemap.xml               # SEO sitemap
├── favicon.ico               # Site icon
├── CNAME                     # GitHub Pages custom domain
├── Gemfile                   # Ruby gem dependencies
├── Gemfile.lock              # Locked gem versions
├── docker-compose.yml        # Local development container
├── README.md                 # Project README
├── LICENSE.md                # License
└── docs/                     # Documentation (excluded from Jekyll build)
    └── kb/                   # Knowledge base articles
```

## Directory Purposes

**_config.yml:**
- Purpose: Jekyll build configuration and site-wide metadata
- Contains: Site title, URL, SASS output settings, compression settings, build exclusions
- Key content: `title`, `url`, `description`, `sass`, `compress_html`, `exclude` (excludes docs/)

**_data/ (data.yml):**
- Purpose: Data-driven resume content
- Contains: All resume sections and content (sidebar info, experiences, education, skills, etc.)
- Key structure:
  - `sidebar`: Personal info, contact, links, profile image
  - `career-profile`: Career summary text
  - `experiences`: Array of work roles with company and details
  - `education`: Array of degrees
  - `skills`: Categories of technical skills
  - `certifications`: Array of certs/training
  - `projects`: Array of project assignments
  - `recommendations`: Array of testimonials
  - `highlights`: Array of career achievements
- Format: YAML key-value tree; all values rendered via Liquid in templates

**_layouts/:**
- Purpose: Top-level HTML structure templates
- `default.html`: Web layout with sidebar, action buttons, theme toggle, print/PDF buttons
- `print.html`: Print-optimized layout; forces light theme, hides buttons, full-width wrapper
- `compress.html`: Minified HTML output (defined but unused)

**_includes/:**
- Purpose: Reusable HTML components for resume sections
- Pattern: Each file renders one section; iterates over `site.data.data.<section>` and emits HTML
- Key files:
  - `head.html`: Meta, fonts, styles, JSON-LD structured data (profile schema)
  - `sidebar.html`: Profile image, name, tagline, contact, conditional education/interests
  - `*-section.html`: Career profile, highlights, experiences, education, certifications, skills, recommendations, projects
  - `contact.html`: Email, phone, social links (LinkedIn, GitHub, etc.)
  - `footer.html`: Attribution footer
  - `scripts.html`: Theme toggle event listener and localStorage persistence

**_sass/:**
- Purpose: Stylesheets for web and print rendering
- `_tokens.scss`: CSS custom properties for dark/light color schemes (--bg, --text, --accent, etc.)
- `_base.scss`: Base typography, spacing, layout structure (flexbox grid, sidebar/content wrapper)
- `_default.scss`: Component styles for all sections (sections, items, contact info, skills tags, buttons)
- `_responsive.scss`: Media queries for mobile (<600px), tablet, desktop breakpoints
- `_utilities.scss`: Utility classes (margin, padding, text alignment)
- `_print.scss`: Media query `@media print` with print-specific overrides (hide buttons, adjust spacing, light theme colors)

**assets/css/:**
- Purpose: Compiled CSS stylesheet
- `main.scss`: Entry point for SCSS compilation; defines sidebar order via Liquid front matter, imports all partials
- Output: `main.css` (generated by Jekyll during build)

**assets/js/:**
- Purpose: Client-side JavaScript
- `pdf-generator.js`: Opens `/print` page in new window for PDF/print export

**assets/images/:**
- Purpose: Static images
- `profile.png`: Avatar image (100x100px, referenced in sidebar)

**assets/plugins/:**
- Purpose: External dependencies
- `bootstrap/`: Bootstrap CSS framework (grid, utilities)
- `font-awesome/`: Font Awesome icon library (section icons)

**index.html:**
- Purpose: Main web entry point
- Contains: Front matter (`layout: default`) and include statements for all sections
- Renders: Full interactive resume with sidebar, theme toggle, action buttons

**print.html:**
- Purpose: Print/PDF entry point
- Contains: Front matter (`layout: print`, `permalink: /print`) and same section includes as index.html
- Renders: Same content as web, but with print layout and light theme

**robots.txt, sitemap.xml:**
- Purpose: SEO metadata
- robots.txt: Allows all crawlers
- sitemap.xml: Lists all URLs (static; only index.html and print.html)

**CNAME:**
- Purpose: GitHub Pages custom domain
- Contains: `career.secop.dev`

**Gemfile / Gemfile.lock:**
- Purpose: Ruby dependencies
- Contains: Jekyll, github-pages, webrick gems
- Lock file: Ensures reproducible builds

**docker-compose.yml:**
- Purpose: Local development container
- Contains: Jekyll development server setup for local testing

**docs/:**
- Purpose: Project documentation (excluded from build)
- Excluded: `_config.yml` has `exclude: [docs/]` so docs/ doesn't appear in built site

## Key File Locations

**Entry Points:**
- `index.html`: Main web entry point (https://career.secop.dev/)
- `print.html`: Print/PDF entry point (https://career.secop.dev/print)

**Configuration:**
- `_config.yml`: Site title, URL, Jekyll build settings, SASS config

**Data Source:**
- `_data/data.yml`: All resume content (single source of truth)

**Core Logic:**
- `_layouts/default.html`: Main layout structure and component orchestration
- `_layouts/print.html`: Print layout structure
- `index.html`: Page structure (includes all sections in order)
- `print.html`: Print page structure (same sections, print layout)

**Templates/Components:**
- `_includes/head.html`: Meta tags, fonts, styles, structured data
- `_includes/sidebar.html`: Profile and contact sidebar
- `_includes/experiences.html`: Work experience rendering
- `_includes/skills.html`: Technical skills rendering
- `_includes/*.html`: Each section has a corresponding include

**Styling:**
- `assets/css/main.scss`: SCSS entry point
- `_sass/_tokens.scss`: Design tokens (dark/light colors)
- `_sass/_base.scss`: Base styles, layout structure
- `_sass/_default.scss`: Component styles
- `_sass/_responsive.scss`: Media queries
- `_sass/_print.scss`: Print overrides

**Behavior:**
- `assets/js/pdf-generator.js`: PDF/print button handler
- `_includes/scripts.html`: Theme toggle logic and localStorage

## Naming Conventions

**Files:**
- Layout files: kebab-case, prefixed with underscore (`_layouts/default.html`, `_layouts/print.html`)
- Include files: kebab-case, prefixed with underscore (`_includes/career-profile.html`)
- SCSS files: kebab-case, prefixed with underscore (`_sass/_base.scss`)
- Data files: kebab-case (`_data/data.yml`)
- Page entry points: kebab-case, no underscore (`index.html`, `print.html`)
- Config files: lowercase, no space (`_config.yml`, `docker-compose.yml`)

**Directories:**
- Jekyll special directories: lowercase, prefixed with underscore (`_config`, `_data`, `_layouts`, `_includes`, `_sass`)
- Asset directories: lowercase, no underscore (`assets/`)
- Subdirectories: lowercase, semantic naming (`assets/css/`, `assets/js/`, `assets/plugins/`, `assets/images/`)

**CSS Classes:**
- Kebab-case: `.sidebar-wrapper`, `.profile-container`, `.section-title`, `.skill-tag`
- BEM-like: `.section .item .details` (semantic nesting, not strict BEM)
- Utility classes: Bootstrap-based (`.d-print-none`, `.d-flex`, `.mb-3`)

**Liquid Variables:**
- CamelCase: `sidebar`, `experiences`, `skills` (from YAML keys)
- Kebab-case: Loop variables (`experience`, `skill`, `category`, `item`)
- Site global: `site.data.data.<section>`, `site.baseurl`, `site.title`

**YAML Keys (in _data/data.yml):**
- kebab-case: `sidebar`, `career-profile`, `career-highlights`, `experiences`, `skills`, `recommendations`
- Nested fields: `sidebar.name`, `sidebar.avatar`, `experiences.info`, `skills.categories`

## Where to Add New Code

**New Resume Section:**
1. Add data to `_data/data.yml` under a new section key (e.g., `publications`, `awards`)
2. Create `_includes/<section-name>.html` with template:
   ```liquid
   {% assign <section> = site.data.data.<section-name> %}
   {% if <section> %}
   <section class="section <section-name>-section">
     <h2 class="section-title"><i class="fas fa-<icon>"></i>{{ <section>.title }}</h2>
     {% for item in <section>.info %}
       <div class="item">
         <!-- Render item -->
       </div>
     {% endfor %}
   </section>
   {% endif %}
   ```
3. Include the section in `index.html` and `print.html`: `{% include <section-name>.html %}`
4. Add SCSS styles to `_sass/_default.scss` for `.section.<section-name>-section`, `.item`, and nested elements
5. If section is conditional (sidebar vs. main), add a flag to `_data/data.yml` (e.g., `sidebar.<section>: True/False`) and wrap include in `{% if site.data.data.sidebar.<section> %}` in sidebar or main page

**New Styling Feature:**
1. Add CSS custom properties to `_sass/_tokens.scss` (both `:root` dark and `[data-theme="light"]` blocks) for new colors
2. Add component styles to `_sass/_default.scss` for new element classes
3. Add responsive breakpoints to `_sass/_responsive.scss` if needed
4. Add print overrides to `_sass/_print.scss` if the feature needs print-specific styling
5. Do NOT edit inline styles in layout files; keep all styles in SCSS files

**New Interactive Feature:**
1. Add JavaScript to `assets/js/<feature-name>.js` (vanilla JS, no frameworks)
2. Include the script in `_includes/scripts.html` or `_layouts/default.html` after theme-toggle script
3. Use localStorage for persistence (like theme toggle does)
4. Add no-print class (`.d-print-none`) to buttons/interactive elements so they hide in print view
5. Test in both web and print paths

**New Sidebar Contact Link:**
1. Add field to `_data/data.yml` under `sidebar` (e.g., `bluesky`, `mastodon`)
2. Add conditional render to `_includes/contact.html`:
   ```liquid
   {% if sidebar.<field> %}
     <a href="[URL pattern]" target="_blank" rel="nofollow"><i class="fab fa-<icon>"></i></a>
   {% endif %}
   ```
3. Add CSS styles to `_sass/_default.scss` for link appearance
4. Update `_includes/head.html` schema.org `sameAs` array if it's a social profile

**New Theme Color:**
1. Add new custom property to `_sass/_tokens.scss` in both `:root` (dark value) and `[data-theme="light"]` (light value)
2. Use the property in SCSS files via `var(--<new-property>)`
3. Example:
   ```scss
   // In _sass/_tokens.scss
   --new-color: #abc123;  // dark value
   [data-theme="light"] { --new-color: #def456; }  // light value
   
   // In _sass/_default.scss
   .element { color: var(--new-color); }
   ```

## Special Directories

**_config.yml (Jekyll special):**
- Purpose: Build configuration and site metadata
- Generated: No
- Committed: Yes
- Notes: Central point for site-wide settings; changes here rebuild the entire site

**_data/ (Jekyll special):**
- Purpose: YAML/JSON data source for templates
- Generated: No (hand-authored)
- Committed: Yes
- Notes: `data.yml` is the single source of truth; no database or external API

**_layouts/ (Jekyll special):**
- Purpose: Top-level template wrappers
- Generated: No
- Committed: Yes
- Notes: Each page file (`index.html`, `print.html`) specifies its layout in front matter

**_includes/ (Jekyll special):**
- Purpose: Reusable template partials
- Generated: No
- Committed: Yes
- Notes: Included via Liquid `{% include %}` syntax; form the building blocks of pages

**_sass/ (Jekyll special):**
- Purpose: SCSS source files
- Generated: No
- Committed: Yes
- Notes: Compiled to `assets/css/main.css` by Jekyll during build

**assets/ (public):**
- Purpose: Static resources (CSS, JS, images)
- Generated: `main.css` (compiled from `_sass/` and `assets/css/main.scss`)
- Committed: Yes (CSS/JS committed; CSS is compiled but committed for availability)
- Notes: Served as-is by Jekyll; `main.css` is compiled once per build

**.planning/ (documentation):**
- Purpose: GSD planning and codebase analysis
- Generated: Yes (populated by GSD agents)
- Committed: Yes
- Notes: Excluded from Jekyll build; doesn't affect site output

**docs/ (documentation):**
- Purpose: Project documentation
- Generated: No
- Committed: Yes
- Notes: Excluded from Jekyll build (`_config.yml` has `exclude: [docs/]`); useful for developers but not published

---

*Structure analysis: 2026-08-05*
