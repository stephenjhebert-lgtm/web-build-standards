# CLAUDE.md — Reps & Reels

**Purpose:** Site-specific context and overrides for the Reps & Reels Webflow build. Read this after the global standards, before touching anything.

**Read order for any Reps & Reels web task:**
1. Global Webflow MCP Field Guide: https://raw.githubusercontent.com/stephenjhebert-lgtm/web-build-standards/main/standards/webflow/mcp-field-guide.md
2. This file
3. `./README.md` — brand registry entry (site IDs, status, links)
4. The live site's working files in iCloud: `~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/Reps and Reels/web/`
   - `SOURCE_OF_TRUTH.md` — authoritative running state (page structure, assets, inline-style log, version history)
   - `webflow_head_paste.html` — source of truth for all custom CSS pasted into Webflow Site Head
   - `homepage_prototype.html` — design reference
   - `homepage_copy.md` — copy deck

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

- **Custom code → Head is the source of truth for styling.** The canonical file is `webflow_head_paste.html` in the iCloud project folder (~834 lines), containing all `@font-face` rules, `:root` tokens, component styles, responsive `@media` blocks, and keyframes. Never hand-edit in Webflow without syncing back to this file.
- **Hero atmosphere uses a helper div (`.hero__atmosphere`)** — required because `style_tool` rejected multi-gradient `background-image` on the `.hero` class at `main`. The stacked radial gradient is carried in the injected head CSS, not on the hero class.
- **Marquee dark text uses an injected helper** (`.rr-marquee-dark-text`, `aria-hidden`) carrying `.marquee .marquee__item { color:#0b0b0c }` because class-level `color` updates were failing silently.
- **H1 accent coloring on "Pipes"** uses a span (`inline-span-0`) with the H1 brass (`#E6BB5A`), applied structurally per field guide Part 5.1.
- **Full page structure, assets, OG tags, and mobile behavior** are documented in `SOURCE_OF_TRUTH.md` in the iCloud folder. Don't duplicate that here — edit it there.

## Known inline-style workarounds

Kept in sync with `SOURCE_OF_TRUTH.md`. Every one exists because Webflow's `style_tool` silently rejected the class update at `main`.

| Element | Property | Reason | Date |
|---|---|---|---|
| Nav mark image | `height:44px; width:auto; max-width:72px; filter: drop-shadow(…brass glow…)` | Filter/size on `main` rejected | 2026-04-20 |
| Footer mark image | `height:36px; width:auto; max-width:56px; filter: drop-shadow(…)` | Same | 2026-04-20 |
| Marquee section | `background-color:#c9a24b; border-top-color/bottom-color` | Class `background-color` rejected | 2026-04-20 |
| Stats-band numbers (×3) | `color:#6B1E1E` | Class `color` rejected | 2026-04-20 |
| Voice cornerstone | `border-left:6px solid #6B1E1E; padding-left:20px; …` | Class `border-*` rejected | 2026-04-20 |

## Human-in-the-loop queue

- [ ] Point `repsreels.com` custom domain (DNS + Webflow domain connect)
- [ ] Replace `[ Reel embed ]` placeholders with live IG/TT embeds (see `IG_TT_INTEGRATION.md` in iCloud folder)
- [ ] Paste Omnisend newsletter form embed into contact section
- [ ] Decide on sticker / tattoo crest variant (2026-04-20 audit item)
