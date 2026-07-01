# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A static personal website for **Curious Head Labs** (www.curiousheadlabs.com), hosted via GitHub Pages (CNAME). No build step, no framework, no package manager — every file is plain HTML/CSS/JS served directly.

## Serving locally

```sh
python3 -m http.server 3000
# then open http://localhost:3000
```

There is no build, lint, or test command. Changes to `.html` files are immediately visible on refresh.

## Architecture

### `index.html` — main site

A single-file SPA. Navigation between pages (Home, Projects, Contact, Hire) is handled entirely by client-side hash routing:

- `navigate(page)` toggles `.active` on `#page-<name>` divs and updates `location.hash`
- `#app` is hidden until `initRouter()` runs (prevents FOUC)
- Projects are rendered dynamically from the `projs` array in the `<script>` block — adding or editing a project means editing that array

Design tokens live in `:root` CSS variables. The site is a **single light theme** (warm off-white `--bg: #f7f6f2`) — there is no dark mode / no `prefers-color-scheme` override. Icons come from Phosphor Bold (`@phosphor-icons/web`). Fonts are Plus Jakarta Sans (body) and Newsreader (italic hero text), both via Google Fonts.

The home page is **immersive**: an animated neural-network `<canvas id="neural">` reacts to the cursor (`initNeural()`), a typewriter cycles the hero italic (`type()`), and an interactive "Ask the lab" prompt (`initAsk()` / `askRun()`) streams a keyword-matched answer with a CTA — `askAnswers` holds the responses, `matchAnswer()` routes free text. Layout is **full-bleed**: `.page` spans full width with fluid `--pad` padding, capped at `--maxw: 1600px`; the hero fills the viewport.

### `elio/index.html` — Elio sub-app

A self-contained frontend for the Elio "Personal Knowledge Archaeologist" product. It has its own tab-based navigation (Dashboard, Chat, About) driven by `switchView()`.

**Backend dependency**: `const API = 'http://localhost:8080'` — all fetch calls point there. The backend must be running locally for Chat and Dashboard to work. Auth is Google OAuth via the backend's `/oauth2/authorization/google` redirect. Ingest is triggered via `POST /ingest/gmail`. The dashboard fetches entities from `GET /entities?type=<tasks|people|projects>&limit=<n>`.

Icons in Elio use Tabler Icons (`@tabler/icons-webfont`), unlike the main site which uses Phosphor.

### `design-preview.html`

A standalone design scratchpad — not linked from the main site, used for visual iteration.

## Skills

Design skills are installed in `.agents/skills/` and tracked in `skills-lock.json`. The active design vocabulary for this project is drawn from those skills (Leonxlnx/taste-skill). When making UI changes, the `design-taste-frontend`, `high-end-visual-design`, or `minimalist-ui` skills are the relevant references for stylistic decisions.

## Design conventions

- Accent color is purple (`--p: #6d64d6`) used only as a tint/accent (neural net, links, spotlight) — never as a full-surface CTA background
- CTAs and primary buttons use `var(--tx)` (near-black) with `var(--bg)` text; hover is `#2a2927`
- Layout width is full-bleed: `--pad` (fluid side padding) capped at `--maxw: 1600px`; `--max: 880px` remains for narrow text blocks
- Border radius: `--r: 8px` (components), `--rl: 14px` (cards)
- The grain overlay (`.grain`, SVG noise via `background-image`) is a deliberate visual texture — do not remove it
