# CLAUDE.md — Reps & Reels

**Purpose:** Site-specific context and overrides for the Reps & Reels Webflow build. Read this after the global standards, before touching anything.

**Read order for any Reps & Reels web task:**
1. Global Webflow MCP Field Guide: https://raw.githubusercontent.com/stephenjhebert-lgtm/web-build-standards/main/standards/webflow/mcp-field-guide.md
2. This file
3. `./README.md` — brand registry entry (site IDs, status, links)
4. `./SOURCE_OF_TRUTH.md` — authoritative running state (page structure, assets, inline-style log, version history)
5. `./webflow_head_paste.html` — source of truth for all custom CSS pasted into Webflow Site Head

Historical and learning context lives alongside those files:
- `./POSTMORTEMS.md` — incidents, root causes, preventions
- `./KEY_INSIGHTS.md` — durable lessons worth carrying to the next project
- `./UPDATE_HISTORY.md` — higher-altitude changelog (SOURCE_OF_TRUTH covers page-level state)
- `./archive/` — frozen per-round build logs, fix prompts, QA passes, superseded drafts

The local iCloud folder (`~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/Reps and Reels/web/`) contains only pointer stubs and local-only state (`.claude/launch.json`). Edit everything else here in the repo.

---

## Where to put new work

| If you are recording… | Put it in |
|---|---|
| Current page structure, assets, inline-style workarounds | `SOURCE_OF_TRUTH.md` |
| A blame-free writeup of something that went wrong | `POSTMORTEMS.md` |
| A durable lesson worth remembering on the next project | `KEY_INSIGHTS.md` |
| A meaningful shift in site state, setup, or process | `UPDATE_HISTORY.md` |
| A frozen reference of a superseded draft or build round | `archive/` (dated folder; do not overwrite in place) |
| A new per-page spec, copy deck, or integration spec | Top-level markdown file in this folder |

When a decision is reversed or a draft is superseded, move the old version under `archive/superseded_drafts/` or a dated build folder. Never overwrite in place — the archive's completeness is the audit trail.

---

## Site identity

- **Brand:** Reps & Reels
- **Webflow site ID:** `6932246fda6d6f12bcacc35f`
- **Home component ID:** `69322470da6d6f12bcacc3bb`
- **Staging URL:** `repsreels.webflow.io`
- **Custom domain:** `repsreels.com` — not yet pointed
- **Live URL:** [to fill in once custom domain is connected]

## Brand tokens

| Token | Hex | Usage |
|---|---|---|
| Forge Black | `#0B0B0C` | Dominant background |
| Bone Paper | `#F1ECE1` | Body text on dark; light section backgrounds |
| Aged Brass | `#C9A24B` | Primary accent — marquee, nav tagline, rules, mono labels |
| Brass (H1) | `#E6BB5A` | H1 accent word (`Pipes`) only |
| Oxblood | `#6B1E1E` | Secondary accent — stats numerals, voice cornerstone border, about H2 accent |
| Charcoal | `#2A2A2A` | Layering, reel card backgrounds |

### Typography

- **Display / headlines:** Big Shoulders Display (800/900) — condensed heavy sans, all-caps, tight tracking
- **Body:** Fustat (400/500/600) — humanist sans with letterpress feel
- **Mono (labels, chip, nav tagline):** IBM Plex Mono (500/600)

## Site-specific quirks

- **Custom code → Head is the source of truth for styling.** The canonical file is `./webflow_head_paste.html` (~834 lines), containing all `@font-face` rules, `:root` tokens, component styles, responsive `@media` blocks, and keyframes. Never hand-edit in Webflow without syncing back to this file.
- **Hero atmosphere uses a helper div (`.hero__atmosphere`)** — required because `style_tool` rejected multi-gradient `background-image` on the `.hero` class at `main`. The stacked radial gradient is carried in the injected head CSS, not on the hero class.
- **Marquee dark text uses an injected helper** (`.rr-marquee-dark-text`, `aria-hidden`) carrying `.marquee .marquee__item { color:#0b0b0c }` because class-level `color` updates were failing silently.
- **H1 accent coloring on "Pipes"** uses a span (`inline-span-0`) with the H1 brass (`#E6BB5A`), applied structurally per field guide Part 5.1.
- **Full page structure, assets, OG tags, and mobile behavior** are documented in `SOURCE_OF_TRUTH.md`. Don't duplicate that here — edit it there.

## Known inline-style workarounds

Kept in sync with `SOURCE_OF_TRUTH.md`. Every one exists because Webflow's `style_tool` silently rejected the class update at `main`.

| Element | Property | Reason | Date |
|---|---|---|---|
| Nav mark image | `height:44px; width:auto; max-width:72px; filter: drop-shadow(…brass glow…)` | Filter/size on `main` rejected | 2026-04-20 |
| Footer mark image | `height:36px; width:auto; max-width:56px; filter: drop-shadow(…)` | Same | 2026-04-20 |
| Marquee section | `background-color:#c9a24b; border-top-color/bottom-color` | Class `background-color` rejected | 2026-04-20 |
| Stats-band numbers (×3) | `color:#6B1E1E` | Class `color` rejected | 2026-04-20 |
| Voice cornerstone | `border-left:6px solid #6B1E1E; padding-left:20px; …` | Class `border-*` rejected | 2026-04-20 |

See `POSTMORTEMS.md` ("Webflow `style_tool` silently rejects class-level updates") for the pattern and the escape hatch.

## Human-in-the-loop queue

- [ ] Point `repsreels.com` custom domain (DNS + Webflow domain connect)
- [ ] Replace `[ Reel embed ]` placeholders with live IG/TT embeds (see `IG_TT_INTEGRATION.md`)
- [ ] Paste Omnisend newsletter form embed into contact section
- [ ] Decide on sticker / tattoo crest variant (2026-04-20 audit item)
