# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal academic website for Stefano Giglio (Yale SOM), hosted via GitHub Pages at `stefano-giglio.github.io`. The goal is to replicate and improve upon the content from the existing Google Sites page (https://sites.google.com/view/stefanogiglio/).

## Architecture

This is a **static single-page site** — no build step, no framework, no dependencies.

- **`index.html`** — the entire website: HTML, CSS (in `<style>`), and JS (in `<script>`) are all in this one file
- **`images/`** — headshot (`sg.jpg`), Yale SOM logo and shield
- **`papers/`** — PDFs of research papers, appendices, and CV
- **`slides/`** — presentation decks, with their own `index.html`

### How the page works

The site uses a **sidebar + section** layout with JavaScript-driven navigation (no page reloads). Sections (`#home`, `#working-papers`, `#publications`, `#data`, `#discussions`, `#teaching`, `#contact`, `#outside-activities`, plus topic sub-pages) are toggled via `showSection()` which adds/removes the `.active` class. Only one section is visible at a time.

CSS variables are defined in `:root` for colors, fonts, and layout dimensions. The design uses two Google Fonts: Source Serif 4 (headings) and Source Sans 3 (body).

Mobile responsiveness: at ≤900px the sidebar collapses into a hamburger menu with an overlay.

## Updating a paper (IMPORTANT — follow every step)

When Stefano supplies a new version of an existing paper, all four steps below are required. A paper appears in **two** places on the site — its main-list entry (Working Papers *or* Publications) **and** its thematic topic page — and both must be kept in sync.

### 1. Read the date off the PDF's title page

Do **not** guess the date from the filename, the git log, or what the site currently says. Open the new PDF and read the date printed on the front page — that is the authoritative date.

```
pdftotext -f 1 -l 1 papers/NEW.pdf - | head -30
```

This also gives you the current title and abstract to copy across. If `pdftotext` is unavailable, read the PDF's first page directly with the Read tool.

Note the title page gives a full date (e.g. `July 1, 2026`) but the site shows month and year only (`July 2026`).

### 2. Add the new file, remove the old one

Filenames are lowercase, hyphenated: `lastname-lastname-YYYY.pdf`, where `YYYY` is the **year from the title page**. So if the old file was `xyz_2023.pdf` and the new title page says 2025, the new file is `xyz-2025.pdf` and `xyz_2023.pdf` must be deleted (`git rm`) — never leave a stale version in `papers/`.

- Once a paper is **published**, its filename gains a journal slug: `campbell-giglio-pathak-aer-2011.pdf`, `dew-becker-giglio-rfs-2016.pdf`. Forthcoming papers keep the working-paper form.
- If the paper and appendix are separate PDFs, the appendix is the same stem plus `-appendix`: `xyz-2025.pdf` and `xyz-2025-appendix.pdf`. Rename the old appendix too.

### 3. Update the main-list entry in `index.html`

Update the **title, author line, date, abstract, and every `href`** — a renamed PDF means the `href` in both places is now broken until changed. Grep for the old filename stem to be sure nothing still points at it.

**Working papers** (`#working-papers`) use `.data-card`, carry an abstract, and show **month and year**:

```html
<div class="data-card">
    <div class="data-card-title"><a href="papers/xyz-2025.pdf">Title</a></div>
    <div class="data-card-desc">with Coauthor One and Coauthor Two, March 2025</div>
    <div class="data-card-links">
        <button class="abstract-toggle" onclick="toggleAbstract(this)">Abstract</button>
        <a href="papers/xyz-2025-appendix.pdf">Online Appendix</a>
    </div>
    <div class="paper-abstract">Full abstract text…</div>
</div>
```

**Publications** (`#publications`) use numbered `.paper` blocks, carry **no abstract**, and show **year only** — never a month — inside the citation:

```html
<div class="paper">
    <div class="paper-number">36.</div>
    <div class="paper-title"><a href="papers/xyz-jf-2025.pdf">Title</a></div>
    <div class="paper-authors">with Coauthor One and Coauthor Two</div>
    <div class="paper-journal"><strong>Journal of Finance</strong> (2025), 80(3): 1201–1245</div>
</div>
```

Use the full citation when volume/issue/pages are known; otherwise `<strong>Journal</strong>, forthcoming`. Page ranges use an en dash (`–`), not a hyphen. See "Working paper → publication" below for the full promotion procedure.

### 4. Update the thematic topic page

Every paper also lives in one of `#topic-climate`, `#topic-beliefs`, `#topic-factors`, `#topic-dynamics`, `#topic-volatility`, `#topic-real-estate`. These use `.paper.topic-paper`, carry **no abstract**, and follow the same date rule (month + year while a working paper, year-only citation once published):

```html
<div class="paper topic-paper">
    <div class="paper-title"><a href="papers/xyz-2025.pdf">Title</a></div>
    <div class="paper-authors">with Coauthor One and Coauthor Two, March 2025</div>
    <div class="paper-links">
        <a href="papers/xyz-2025-appendix.pdf">Online Appendix</a>
    </div>
</div>
```

Drop the `<div class="paper-links">` wrapper entirely when there is no separate appendix.

## Working paper → publication

When a paper is accepted, Stefano supplies **the link to the journal's article page**. That page is the source of truth for both the PDF and the citation.

### 1. Take the PDF from the publisher's page

Use the **published version of record** from the journal site, not the working-paper PDF already in `papers/`. The published PDF replaces the old one — `git rm` the working-paper file.

Grab the appendix from the same page if the journal hosts it separately (often "Supplementary Material" / "Internet Appendix").

**Publisher PDFs are frequently paywalled.** Verify the download is a real PDF and not a login page or an HTML stub:

```
file papers/NEW.pdf && ls -lh papers/NEW.pdf     # expect "PDF document", not "HTML"; not a few KB
```

If the PDF is gated, **stop and ask Stefano to supply the file** — Yale's subscription makes it reachable in his browser but not from here. Do **not** silently keep the working-paper PDF under a published-looking filename; that leaves the wrong version of the paper on the site.

### 2. Read the citation off the article page

Take year, volume, issue, and page range from the journal page — not from the PDF header, and not from memory. Format it exactly like the existing entries:

```html
<div class="paper-journal"><strong>Journal of Finance</strong> (2025), 80(1): 259–319</div>
```

If the article is accepted but not yet assigned a volume/issue, use `<strong>Journal of Finance</strong>, forthcoming` and revisit once it is paginated.

### 3. Rename with the journal slug

Published filenames add a journal slug before the year, using the **publication** year: `xyz-2025.pdf` → `xyz-jf-2025.pdf`. Slugs already in use:

| Slug | Journal | Slug | Journal |
|---|---|---|---|
| `jf` | Journal of Finance | `qje` | Quarterly Journal of Economics |
| `jfe` | Journal of Financial Economics | `jpe` | Journal of Political Economy |
| `rfs` | Review of Financial Studies | `jme` | Journal of Monetary Economics |
| `res` | Review of Economic Studies | `aer` | American Economic Review |
| `raps` | Review of Asset Pricing Studies | `aej` | AEJ: Macroeconomics |
| `arfe` | Annual Review of Financial Economics | `aer-pp` | AEA Papers and Proceedings |
| `rof` | Review of Finance | `jasa` | Journal of the American Statistical Association |

**Forthcoming papers have no publication year yet**, so name them with the slug and no year: `giglio-xiu-zhang-jasa.pdf`, `giglio-kuchler-stroebel-wang-jfip.pdf`. Add the year only once the issue is assigned.

**Watch for filename collisions between different papers by the same authors.** `giglio-dew-becker-molavi-2025.pdf` and `giglio-dew-becker-molavi-2026.pdf` are two *different* papers, distinguished only by year. Before overwriting or deleting, confirm which paper a file actually is (`pdftotext -f 1 -l 1 …`) rather than assuming the year-stamped name refers to the paper you're updating.

### 4. Move the entry between sections

- **Delete** the paper's `.data-card` from `#working-papers`.
- **Add** a `.paper` block at the top of `#publications` and **renumber every entry below it** (`.paper-number` runs highest-first, so the new paper takes the next number up and the rest shift by one).
- The abstract is **dropped** — Publications entries carry no `.paper-abstract`.
- The month is **dropped** — the date now lives in the citation as a year only.
- Keep any `.paper-award` line; awards carry over.

### 5. Update the topic page

Swap the working-paper date line for the citation. The `.paper-authors` line loses its trailing month/year, and a `.paper-journal` line is added:

```html
<div class="paper topic-paper">
    <div class="paper-title"><a href="papers/xyz-jf-2025.pdf">Title</a></div>
    <div class="paper-authors">with Coauthor One and Coauthor Two</div>
    <div class="paper-journal"><strong>Journal of Finance</strong> (2025), 80(1): 259–319</div>
</div>
```

### Verify before finishing

```
grep -n "OLD-STEM" index.html        # must return nothing
grep -o 'papers/[^"]*\.pdf' index.html | sort -u | while read f; do [ -f "$f" ] || echo "BROKEN: $f"; done
```

Escape `&` as `&amp;` in titles and abstracts.

## Development

Open `index.html` directly in a browser — no server needed. For live-reload during development:

```
python3 -m http.server 8000
```

There are no build tools, linters, tests, or package managers.

## Deployment

Push to `main` branch — GitHub Pages serves the site automatically.

## Current State & Known Gaps

- All 35 publications (plus working papers) are listed with local PDFs in `papers/`
- Several external press/media links are broken (VoxEU migrated to CEPR, old Chicago Booth and BFI URLs are dead)
- The owner wants to tinker with the aesthetic — design changes are welcome
