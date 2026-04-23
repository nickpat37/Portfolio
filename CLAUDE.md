# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working in this repository.

## Project Overview

Nick Pat's UX/Branding portfolio website — a single-page static site built with vanilla HTML, CSS, and JavaScript. No build system, no package manager, no dependencies. The entire application lives in `index.html`.

## Development Workflow

**Local development:** Open `index.html` directly in a browser, or serve with any static server:
```bash
python3 -m http.server 8080
# or
npx serve .
```

**Deployment:** Push to git → Vercel auto-deploys. No build step required.

There are no test, lint, or build commands — the project is zero-toolchain by design.

## Architecture

**Single-file SPA:** All HTML structure, embedded `<style>` CSS, and `<script>` JavaScript are in `index.html`. Assets live in `assets/`.

### Key Data Structures (JS)

- `METRICS` — per-project key metrics displayed in case study overlays
- `GALLERY` — image arrays for UX case carousel (`s`, `f`, `p`, `t` keys → project IDs)
- `BRAND_STACKS` — image arrays for brand showcase panels (`b5`–`b8`)
- `HERO_BY_ROLE` — hero copy, project order, labels, and thumbnails for each persona tab (`ux` / `brand`)

### Core Systems

**Role Switching** (`applyHeroRole(role)`): Toggles the hero section between UX and Brand designer personas, reordering project icons and swapping copy. State stored in `currentRole`.

**Case Study Overlay** (`toggle(id)`, `openCaseOverlay()`, `closeCaseOverlay()`): Opens a full-screen overlay panel for a given project. The project header is physically moved into the nav bar (`mountPhToNav`) while the overlay is open, then restored on close.

**Carousel** (`initCaseCarousel(rootId, pid)`): Custom horizontal snap-scroll carousel. Uses `ResizeObserver` to recalculate slide widths on resize and `IntersectionObserver` to track active slide for pagination dots.

**Gallery Modal** (`openGallery(pid)`, `closeGallery()`, `navGallery(dir)`): Full-screen image viewer with thumbnail strip. Keyboard nav: Arrow keys + Escape.

**Brand Showcases** (`initBrandShowcases()`): Lazy-renders brand project image stacks only when the overlay is opened for that project.

### Project IDs

| ID | Project |
|----|---------|
| `s` | Telomeres (UX) |
| `f` | FactWeavers (UX) |
| `p` | ONETemasek (UX) |
| `t` | PAXU (UX) |
| `b5` | Sir Larry in financalland (Brand) |
| `b6` | Weather drink / Daisy Decision (Brand) |
| `b7` | Chinese Theatre Circle (Brand) |
| `b8` | Chevalerie (Brand) |

### Design Tokens (CSS Variables)

Defined in `:root` — `--bg`, `--s`, `--s2`, `--s3` (surfaces), `--bd`/`--bds` (borders), `--t1`/`--t2`/`--t3` (text hierarchy), `--ac` (accent). Typography: `--sans` (Geist) and `--mono` (Geist Mono). Badge colors: `--gd` gold, `--bl` blue, `--gr` green, `--te` teal.

## Asset Conventions

Images live in `assets/images/` named `Project N_*.jpg` (or `.png`/`.gif` for brand projects). Icons in `assets/icons/`. When adding images for a project, update the corresponding array in `GALLERY` or `BRAND_STACKS` in the JS.

## Vercel Config

`vercel.json` holds a single 301 redirect from `/portfolio_rvs.html` to `/`. No other routing configuration needed — Vercel serves `index.html` by default.

## User Preferences

**Read the full file before acting.** `index.html` is a single file — read it completely at the start of any session before making changes. Discovering constraints (overflow, max-width, JS dependencies) reactively mid-task wastes turns.

**Batch independent edits in parallel.** All unrelated CSS or HTML changes should go out as parallel tool calls in a single message, not one-by-one sequentially.

**Clarify ambiguous visual requests before implementing.** If a layout request could mean multiple things (e.g. edge-to-edge vs padded, full-bleed vs contained), ask one targeted question first rather than implement and iterate.

**Don't fetch external sites for design reference.** Common design system knowledge (Apple, Material, etc.) is already known — use it directly without WebFetch.
