# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A **single-file interactive HTML demo** for ConstructFlow — a Hebrew-language (RTL) construction project management SaaS. There is no build step, no npm, no server. Open `constructflow-demo-he.html` directly in a browser or serve via GitHub Pages.

The authoritative product spec is at `docs/superpowers/specs/2026-04-20-construction-pm-app-design.md`. Read it before adding features.

## Architecture

All code lives in `constructflow-demo-he.html`: styles, markup, and scripts in one file.

**Screen system** — four `<div class="screen">` blocks, only one has class `active` at a time:
- `#screen-dashboard` — portfolio overview
- `#screen-initiative` — single job-site view
- `#screen-epic` — epic/stage detail (tasks + feed + cost)
- `#screen-pto` — PTO management

Navigation is driven entirely by `goTo(screen, site, status)` which swaps `active` class and patches text nodes (`init-title`, `init-breadcrumb`, `epic-init-crumb`).

**Modal system** — `openModal(id)` / `closeModal(id)` toggle `.open` on `#<id>-modal` divs and lock body scroll. Current modals: `eod-modal` (EOD report), `worker-modal` (worker quick-contact).

**Worker data** — the `workerData` object in the script section is the single source of truth for worker names, roles, phones, sites, and PTO. The `showWorkerContact(key)` function populates and opens the worker modal. Keys match the first name in Hebrew: `'פאדי'`, `'אחמד'`, `'עמר'`, `'סאמי'`, `'מייק'`.

**Feed tabs** — `switchFeedTab('updates'|'gallery')` toggles between `#feed-content-updates` and `#feed-content-gallery` in Screen 3.

## Design conventions

- **Font**: Heebo (Google Fonts), loaded in `<head>`
- **RTL**: `<html dir="rtl">` — use `margin-inline-start/end` not `left/right` for directional spacing; use `direction:ltr` on number-heavy elements (prices, percentages)
- **Status colors**: green `#22c55e` = complete/on-budget · amber `#f59e0b` = in-progress/minor · red `#ef4444` = stalled/overrun · blue `#2563eb` = active/primary
- **Badges**: `.badge-green/red/blue/orange/gray/gold` — reuse existing classes
- **Buttons**: `.btn` base + `.btn-primary/ghost/gold` + `.btn-sm/xs` size modifiers
- **Photos**: Unsplash CDN with `?w=400&h=180&fit=crop&q=80`; always add `onerror="this.style.display='none'"` (security hook blocks `innerHTML` in onerror)

## Site names (Hebrew, localized)

| Screen label | JS parameter |
|---|---|
| דיזנגוף 47 | `'דיזנגוף 47'` |
| רוטשילד 82 | `'רוטשילד 82'` |
| אלנבי 33 | `'אלנבי 33'` |
| בן יהודה 65 | `'בן יהודה 65'` |
| הנמל 8, קיסריה | `'הנמל 8, קיסריה'` |
| הפסגה 12, קיסריה | `'הפסגה 12, קיסריה'` |

## Security hook

A pre-tool hook blocks any edit that contains `innerHTML=` in the diff. Use `setAttribute`, `textContent`, or `classList` instead.
