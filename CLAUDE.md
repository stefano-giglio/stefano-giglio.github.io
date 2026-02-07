# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Personal academic website for Stefano Giglio (Yale SOM), hosted via GitHub Pages at `stefano-giglio.github.io`. The goal is to replicate and improve upon the content from the existing Google Sites page (https://sites.google.com/view/stefanogiglio/).

## Architecture

This is a **static single-page site** — no build step, no framework, no dependencies.

- **`index.html`** — the entire website: HTML, CSS (in `<style>`), and JS (in `<script>`) are all in this one file
- **`images/`** — headshot and other images (currently empty; headshot is hotlinked from Google)
- **`papers/`** — PDFs of research papers, discussion slides, and CV (currently empty; links exist but files not yet added)

### How the page works

The site uses a **sidebar + section** layout with JavaScript-driven navigation (no page reloads). Sections (`#home`, `#research`, `#data`, `#discussions`, `#teaching`, `#contact`) are toggled via `showSection()` which adds/removes the `.active` class. Only one section is visible at a time.

CSS variables are defined in `:root` for colors, fonts, and layout dimensions. The design uses two Google Fonts: Cormorant Garamond (headings) and Source Sans 3 (body).

Mobile responsiveness: at ≤900px the sidebar collapses into a hamburger menu with an overlay.

## Development

Open `index.html` directly in a browser — no server needed. For live-reload during development:

```
python3 -m http.server 8000
```

There are no build tools, linters, tests, or package managers.

## Deployment

Push to `main` branch — GitHub Pages serves the site automatically.

## Current State & Known Gaps

- The `images/` and `papers/` directories are empty — the headshot is hotlinked from Google Sites, and paper PDF links will 404 until files are added
- The `CV/README.md` file was deleted (shown in git status)
- Only publications 26–35 are listed; publications 1–25 are referenced as "available in full CV"
- Discussion slides section only has 3 recent entries
- The owner wants to tinker with the aesthetic — design changes are welcome
