# ATS & Machine-Readability Research — career.secop.dev

Date: 2026-08-18
Status: Research complete, no implementation yet — decisions pending user review.

## Question

Can the site decouple human-optimized visual layout from machine-optimized ATS
parsing, and is a "print mode" actually consumed by ATS scanners?

## Finding 1 — ATS never crawls the live website

There is no official standard or mechanism by which an Applicant Tracking System
(Workday, Taleo/Oracle Recruiting, iCIMS, Greenhouse, Lever, SuccessFactors) fetches
and parses a candidate's personal URL. The ATS's parser only ever runs against the
**file the candidate uploads** (PDF/DOCX) at the point of application. A link to
`career.secop.dev` embedded in that file's text is stored as a contact-info string —
it is never dereferenced or crawled by the ATS itself.

Consequence: `/print` mode being "ATS-optimized" is close to moot — no automated
system fetches and renders that route. Its only real value is as the **source
layout for the human's own browser print-to-PDF**, i.e. it controls what the
*uploaded file* looks like if Sean generates his PDF from that page. That's a real
and correct use — just not because a scanner visits the URL.

## Finding 2 — who actually reads career.secop.dev, and how

1. **Humans** — a recruiter/hiring manager who already has the candidate's résumé
   (via ATS) clicks the personal-site link out of curiosity/verification. This is a
   direct, JS-capable browser visit. Visual design, clarity, and UX matter here —
   full stop, no parsing constraints apply.
2. **Search engine crawlers** (Google, Bing) — index the page for discoverability if
   Sean's name/site is searched directly. These are real "machine readers" of the
   live site, governed by standard SEO practices, not ATS practices.
3. **AI-assisted sourcing tools / recruiter copilots** (LinkedIn Recruiter AI,
   Google/Bing AI overviews, general LLM-based search) — an emerging channel that
   *does* fetch and read the live URL, similar to a crawler but summarizing/
   extracting facts. These benefit from clean semantic HTML and structured data
   (JSON-LD) in the same way a crawler does, since there's no vendor-specific
   parsing quirk to defend against — just "is the content machine-extractable at
   all," which plain semantic HTML already satisfies.
4. **The ATS itself** — never touches the live site (Finding 1). The thing that
   matters for ATS is a separate artifact: the actual uploaded résumé file.

## Finding 3 — real parser behavior on uploaded files (why format still matters)

- Field-level parsing accuracy across vendors tops out around ~87% (vs. ~96% human)
  — even a clean single-column document loses some fields on some parsers.
- Vendors differ materially: Taleo does strict literal keyword matching, iCIMS uses
  ML/semantic matching, Greenhouse doesn't auto-score. The same file produces
  different extracted skill counts per platform.
- Multi-column layouts are the single most damaging format: PDF text extraction
  reads left-to-right across the whole page, interleaving columns
  ("Skills Work Experience SQL Senior Product Manager..."), corrupting every
  downstream field the NER tries to tag.
- PDF vs. DOCX: DOCX parses more consistently on weaker/older parsers (Taleo);
  well-formatted PDF is fine on modern parsers (Greenhouse, Workday). A **simple,
  single-column, text-based PDF** (not a scanned image, no text-in-graphics) is
  safe across virtually all vendors.
- Hyperlinks (LinkedIn, portfolio site) are now reliably captured **only if they
  are real text/anchor links**, not an image or icon-only link.

## Answers

### 1. What do employers/recruiters actually DO with a site like career.secop.dev?

They apply through the employer's job portal, which runs their uploaded résumé
file through the ATS parser — the live website is not part of that pipeline. If
Sean's site link is on the résumé (or LinkedIn/GitHub profile), a human recruiter
may click through to it later, manually, in a browser, once they're already
looking at the candidate. Separately, search engines and some AI-search tools may
index the live page independent of any specific job application.

### 2. How to optimize for the *actual* machine-reading channels

Not "ATS mode" for the live page — that channel doesn't exist. Instead:

- **Treat the downloadable/print-generated PDF as the true ATS-facing artifact.**
  It already exists (`/print` route, per this project's CLAUDE.md — browser
  print-to-PDF, "produces a text-based, ATS-parseable file"). Priorities for that
  specific output: strict single-column linear order, standard section headers
  (Experience / Education / Skills / Certifications), no text rendered as an
  image/icon, real `<a href>` links for LinkedIn/site/email — not icon-only.
- **Optimize the live page for the channels that do read it**: SEO (meta
  description, semantic HTML, sitemap) and JSON-LD structured data using
  `schema.org/Person` (name, jobTitle, alumniOf, sameAs → LinkedIn/GitHub URLs).
  This is invisible to human visual design — pure `<head>`/hidden markup — so it
  is a genuinely free "machine layer" layered on top of the human-optimized page,
  which is the decoupling the user asked about. It is not an ATS feature; it's an
  SEO/AI-search-agent feature.
- Sidebar content (e.g., a new "Continuous Learning" section) is safe either way —
  it's irrelevant to ATS (that channel never sees the live DOM) and fine for SEO/
  crawlers as long as it's real text in sensible DOM order, not an image.

### 3. Enhancements for both human and machine consumption

- Add `schema.org/Person` JSON-LD block to `_layouts/default.html` `<head>` —
  zero visual impact, aids search engines and AI-search agents.
- Confirm `/print` output is strictly single-column and uses real anchor tags for
  all contact/profile links (verify, don't assume — not yet checked in this
  research pass).
- Standard, unambiguous section headings in the print/PDF version — avoid
  creative headers that a parser's keyword matcher won't recognize (e.g. keep
  "Experience," not a stylized alternative) even if the on-screen human layout
  uses friendlier language.
- Add `robots.txt` + `sitemap.xml` if not already present, so search engines
  index the human page.
- Keep everything server-rendered static HTML (already true — Jekyll) — this is
  the single biggest machine-readability win already banked: no JS-only rendering
  means every crawler, ATS-adjacent tool, or AI agent sees full content with zero
  execution required.

## Explicitly out of scope / not recommended

- Building a separate hidden "ATS-only" text dump of the page — pointless, since
  no ATS fetches the URL at all (Finding 1). Don't spend effort decoupling content
  for a channel that isn't real.
- JSON Resume / HR-XML style resume schema — not consumed by real-world ATS
  vendors in practice; not worth the effort for this project.

## Open questions for the user

- Does Sean want the JSON-LD Person block added now (near-zero cost, no visual
  change)? Recommended default: yes.
- Should we audit the current `/print` stylesheet against the single-column /
  real-anchor-link checklist above before adding new content (e.g. Continuous
  Learning)? Not yet done in this session.

## Sources

- [What is an Applicant Tracking System (ATS)? A Full 2026 Guide](https://www.oleeo.com/blog/what-is-an-applicant-tracking-system-ats/)
- [Applicant tracking system — Wikipedia](https://en.wikipedia.org/wiki/Applicant_tracking_system)
- [How Resume Parsers Actually Work: Inside Workday, Greenhouse, Lever, iCIMS, Taleo](https://resumeoptimizerpro.com/blog/how-resume-parsers-actually-work)
- [PDF vs Word Resume for ATS, Which Passes More? (2026)](https://atsverification.com/blog/pdf-vs-word-for-ats/)
- [How Workday, Greenhouse & Taleo Read Your Resume (2026)](https://www.shashiworks.com/ats-workday-greenhouse-taleo.html)
- [ats-screener (open-source ATS parsing simulator)](https://github.com/sunnypatell/ats-screener)
- [Structured data: The recruiter's guide](https://highrise.digital/blog/structured-data-recruiters-guide/)
- [Job Posting Schema: How to Get Your Jobs on Google for Jobs](https://cavuno.com/blog/job-posting-schema)
