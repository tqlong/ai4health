# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static, single-page conference website for AI4HEALTH 2026 (Hanoi, 23–24 Oct 2026), served at https://ai4health.org.vn via GitHub Pages (`CNAME`). No build step, no package manager, no tests, no framework: just `index.html`, `styles.css`, `script.js`, plus `img/` and `asset/` (official announcement PDFs linked from the page).

To preview locally, serve the directory over HTTP (e.g. `python3 -m http.server`) and open `index.html`; `file://` works too, except `localStorage` may be unavailable (the code tolerates that).

## Bilingual content model (the important part)

The site is Vietnamese-first with an English toggle, all done client-side in `script.js`. Any content change must be made in **both** languages or the EN/VI views will drift.

- **Static markup** lives in `index.html` with Vietnamese text as the default. Elements carry `data-i18n="key"` (text content) or `data-i18n-aria-label` / `data-i18n-alt` / `data-i18n-content` / `data-i18n-href` (attributes). `render(lang)` looks each key up in `I18N[lang]`, falling back to `I18N.en`, then empty string. So adding a new `data-i18n` key requires adding it to both `I18N.en` and `I18N.vi`, otherwise VI silently shows the English text.
- **Repeated/structured content** (objectives, topics, deliverables, submission requirements) lives in `CONTENT.en` / `CONTENT.vi` in `script.js` and is rendered into empty containers (`#objectives`, `#topicsGrid`, `#deliverablesGrid`, etc.) by `renderCards` / `renderRequirements`. The HTML containers are placeholders; edit the JS arrays, not the markup.
- **Program** lives in `PROGRAM.en` / `PROGRAM.vi`: blocks (half-day + venue) → parts (session heading, optional time, chairs) → talks `[time | null, title, speaker?]`. A `null` time renders a numbered talk inside a timed parallel session. The source of truth is the official invitation letter (`asset/Thư mời AI4Health 2026.pdf`, pages shown as `asset/invitation-page-*.png`); keep text and images in sync when the program changes.
- **Committees** are a single Vietnamese source of truth in `COMMITTEES` (`[name, affiliation, role]` triples). English is derived at render time: academic titles via regex in `translateCommitteeName` (GS. TS. → Prof. Dr., PGS. TS. → Assoc. Prof. Dr., TS. → Dr., ThS. → MSc.), and roles/affiliations via the lookup tables `COMMITTEE_ROLE_EN` / `COMMITTEE_AFFILIATION_EN`. When adding a member with a new affiliation or role string, add the exact Vietnamese string to the matching table or the EN view will show untranslated Vietnamese. Role keys are matched verbatim, so spelling/diacritic variants ("Uỷ viên" vs "Ủy viên") each need their own entry.
- Language choice persists in `localStorage` under `ai4health-lang`; default is `vi`.

## Other behaviours in `script.js`

- Countdown reads its target from the `datetime` attribute of `#conferenceStart` in `index.html`; change the date there, not in JS.
- Mobile nav toggle and smooth close-on-click are plain DOM code at the bottom of the file.

## Conventions

- Dates and deadlines appear in several places: `<meta description>`/OG tags in `index.html`, hero text, `I18N` strings, and `CONTENT` program/requirements. Update all of them together when the schedule changes.
- Official PDFs in `asset/` have Vietnamese filenames with spaces; links in `index.html` reference them verbatim (with `&amp;`). Keep filenames unchanged or update the links.
- `docx/Domain AI4Health.docx` is gitignored (contains billing details); do not commit it.
- Design tokens are CSS custom properties at the top of `styles.css`; use them rather than hard-coded colors.
