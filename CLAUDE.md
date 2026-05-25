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

---

## Resume Files

Two resume HTML files live in the project root, both using the same IBM Plex Sans CSS template:

- `Resume_NickPham_Senior_UX.html` — Senior UX Designer version
- `resume-pm.html` — Senior Programme Manager version

When working on either file: read it first, batch independent edits in parallel, and hard-refresh the browser to verify (`Cmd + Shift + R`). The local server runs on port 8081 (`python3 -m http.server 8081`).

---

## Resume Writing Preferences

### "Show content first" rule
When the user says "show me the content first" or "don't make changes yet" — output the proposed text in plain markdown only. Do NOT touch the HTML file. Wait for explicit approval before editing.

### Bullet format: Skill → Problem → Action → Result
Every experience bullet follows this structure:

```
**Skill/Capability label:** [problem that existed] by [action taken], [result with metric and timeline].
```

- The **bold label** names the skill or capability (e.g. `**Design system governance:**`, `**Lead end-to-end UX design:**`)
- The problem comes first — what was broken, missing, or failing
- The action is what was done to solve it
- The result closes with a number and/or timeline where possible

**Example:**
> **Design system governance:** resolved handoff friction across projects by architecting a scalable design system and governance model, cutting design-to-handoff cycle by 39% in the first quarter.

### Show what was solved, not what was done
- Lead with the problem identified, not the task performed
- Show ownership and decision-making authority — "made the design call", "stepped in as design lead", "owned UX direction"
- Avoid passive or coordinator-tone verbs: ~~assisted, supported, helped, collaborated on~~
- Use: solved, diagnosed, identified, owned, defined, led, made the call, translated findings into

### Metrics as supporting evidence, not the headline
- The skill and problem come first; metrics validate the claim
- Format: `...driving **47% adoption uplift** and **35% faster task completion** within 6 months`
- Bold all key numbers inline with `<strong>` tags in HTML

### Conciseness rule
Keep each bullet to 1–2 lines maximum. If a sentence can be cut without losing the skill, problem, or result — cut it. When the user says "make it more concise", reduce by at least 30%.

### Grammar fixes to always apply
- "by defined" → "by defining"
- "by led" → "by leading"  
- "by architected" → "by architecting"
- Always fix gerund errors silently without calling them out

### Never use long dashes (em dashes)
Do not use — (em dash) anywhere in resume content or any written output. Use a comma, colon, or rewrite the sentence instead. This applies to all bullets, summaries, section labels, and any text written for Nick.

### XYZ / Google format
Aligns with the formula: *Accomplished X by doing Y, resulting in Z.* Applied to every bullet across all roles, not just the current one being edited.

---

## Research Methodology (Fan-in / Fan-out)

When asked to research and optimise a resume or produce a major analysis:

1. **Fan-out:** Spawn 4 parallel Sonnet agents, each with a distinct research angle (e.g. ATS keywords, resume structure, competitive positioning, hiring manager evaluation)
2. **Fan-in:** Once all 4 complete, pass their consolidated findings to a single Opus synthesizer agent
3. Sonnet agents run in background (`run_in_background: true`); wait for all before launching Opus
4. The Opus agent produces the final output — full HTML file or optimised content

Use model `sonnet` for researchers, `opus` for the synthesizer.

---

## Resume Content Reference

### Nick Pat — Key Facts
- **Current role:** UX Specialist · AI & Data Products at Temus Pte Ltd (Temasek-backed), Feb 2022 – Present
- **Experience:** 6 years end-to-end UX across enterprise, public sector, fintech, AI products
- **Location:** Singapore
- **Contact:** nickpat37@gmail.com | +65 9899 7154 | linkedin.com/in/nickpat/ | nick-pat.com

### Project name mapping
| Resume name | Portfolio ID | Notes |
|---|---|---|
| FactWeavers | `f` | AI-powered analytics platform |
| Laconic Sadar | geospatial | AI geospatial intelligence platform |
| Telomeres | `s` | Health data / lifestyle product |
| ONETemasek | `p` | Civic-scale enterprise platform |
| AIC senior-care platform | — | Government civic services, 200+ pages |
| Sir Larry in Financial Land | `b5` | Financial literacy programme, Stega Capital |

### Skills accent tag rule (sidebar)
Accent blue tags (`class="tag accent"`) = core, demonstrated skills backed by experience bullets.
Grey tags (`class="tag"`) = supporting or contextual skills not directly evidenced in bullets.
Maximum 4–5 accent tags per sidebar section to preserve visual hierarchy.

---

## PDF Export

To export the resume HTML to PDF, use Chrome headless:

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless \
  --disable-gpu \
  --print-to-pdf="/Users/nickpat/Documents/Projects/Portfolio/Resume_NickPham_Senior_UX.pdf" \
  --print-to-pdf-no-header \
  --no-margins \
  "file:///Users/nickpat/Documents/Projects/Portfolio/Resume_NickPham_Senior_UX.html" 2>/dev/null
```

Output path: `/Users/nickpat/Documents/Projects/Portfolio/Resume_NickPham_Senior_UX.pdf`

---

## Resume Layout — One Page Constraint

The resume must fit on **one A4 page**. The current compact CSS values that achieve this:

| CSS property | Value | Line |
|---|---|---|
| `body font-size` | `7.8pt` | 15 |
| `body line-height` | `1.35` | 15 |
| `header padding` | `14px 28px 10px` | 18 |
| `aside padding` | `12px 16px 14px` | 25 |
| `main padding` | varies (user-adjustable) | 26 |
| `section margin-bottom` | `7px` | 28 |
| `aside section margin-bottom` | `9px` | 29 |
| `bullet font-size` | `7.6pt` | 38 |
| `bullet line-height` | `1.35` | 38 |
| `bullet margin-bottom` | `1px` | 38 |
| `job margin-bottom` | `5px` | 31 |
| `job-divider margin` | `5px 0` | 52 |

If content overflows one page, reduce `line-height` on line 38 or `section margin-bottom` on line 28 first — these have the most impact.

---

## Resume Structure Decisions (current)

- **No "Selected Case Studies" section** — removed; portfolio URL in header is sufficient
- **No sidebar Portfolio button** — removed; portfolio URL appears in header contact block
- **Skill-per-bullet rule:** each bullet within a company must show a distinct, non-overlapping skill domain. If bullet 1 mentions research/usability testing, bullet 2 must show a different domain (e.g. design system, leadership, AI capability)
- **Scope context on company line:** each role includes a brief parenthetical e.g. `(sole UX designer)` or `(UX design lead, 2 concurrent product teams)` to signal seniority without adding a bullet

---

## Editing large blocks — use Python, not Edit tool
When replacing multiple bullets or a full section, use a Python `str.replace()` script via Bash rather than multiple Edit tool calls. This avoids file-modified conflicts and is more reliable for multi-line replacements:

```bash
python3 << 'EOF'
with open('/Users/nickpat/Documents/Projects/Portfolio/Resume_NickPham_Senior_UX.html', 'r') as f:
    content = f.read()
content = content.replace(old, new)
with open('/Users/nickpat/Documents/Projects/Portfolio/Resume_NickPham_Senior_UX.html', 'w') as f:
    f.write(content)
print("Done" if old not in content else "FAILED")
EOF
```
