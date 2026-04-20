# Reps & Reels — Site Source of Truth

**Locked:** 2026-04-20
**Staging:** `repsreels.webflow.io` (Webflow subdomain)
**Webflow site ID:** `6932246fda6d6f12bcacc35f`
**Home component ID:** `69322470da6d6f12bcacc3bb`

This document is the authoritative reference for the shipped homepage. If Webflow and this doc disagree, Webflow wins — but file an update PR on this doc the same day. Historical logs, fix rounds, and superseded prompts live under `./archive/`.

---

## What's Live

### Page order (top to bottom)

1. **Skip link** (`.skip-link`) — accessibility anchor to `#main`
2. **Nav** — brass crest mark + "Reps & Reels" wordmark + "Quiet Work Wins." tagline (hidden ≤767px) + IG/TT pills
3. **Hero** (`#main`) — atmosphere glow layer + centered crest image + chip + H1 + subhead + "Keep Reading" cap
4. **Brass marquee strip** — scrolling: TRAIN · MUSIC · DISCIPLINE · QUIET WORK WINS · IRON & REEDS
5. **Stats band** — 20+ Years Under The Bar · 5 Years On The Chanter · 3 Daughters Watching (oxblood numerals)
6. **Voice (01 / The Premise)** — paragraph + oxblood-bordered cornerstone quote
7. **Reels (02 / The Feed)** — 6-card grid of placeholder reel tiles (IG + TT captions)
8. **About (03 / Origin)** — bio paragraphs with oxblood "One person." accent
9. **Contact (04 / Contact)** — `hello@repsreels.com` + Omnisend newsletter placeholder block
10. **Footer** — brass crest mark + "Quiet Work Wins." tagline + IG/TT + email + copyright

### Hero H1 (locked copy)

> **Iron in one hand. [Pipes] in the other.**
>
> "Pipes" carries the brass accent color (`#E6BB5A`) via the `inline-span-0` span.

Subhead: *"Two competitions, same week. Same person. No split-screen, no costume change. The overlap is the whole point."*
Chip: *"A piper. A lifter. One feed."*

### Color system (as shipped)

| Token | Hex | Usage |
|---|---|---|
| Forge Black | `#0B0B0C` | Dominant background |
| Bone Paper | `#F1ECE1` | Body text on dark, section bg for about/contact |
| Aged Brass | `#C9A24B` | Primary accent — marquee bg, nav tagline, rules, mono labels |
| Brass (H1) | `#E6BB5A` | Lighter brass used specifically on H1 accent word ("Pipes") |
| Oxblood | `#6B1E1E` | Secondary accent — stats numerals, voice cornerstone border, about H2 accent |
| Charcoal | `#2A2A2A` | Layering, reel card backgrounds |

### Typography

- **Display / headlines:** condensed heavy sans (varsity block DNA) — all-caps, tight tracking
- **Body:** humanist serif with letterpress feel
- **Mono (labels, chip, nav tagline):** `"IBM Plex Mono", monospace`

### Assets in use

| Slot | Asset | Webflow asset ID |
|---|---|---|
| Nav mark | `RRbrass_alpha.png` | `69e65621508243a47237ad2f` |
| Hero crest | `rr_crest_hero.png` | `69e64dbfb1ae0192090ac1b8` |
| Footer mark | `RRbrass_alpha.png` | `69e65621508243a47237ad2f` |
| Open Graph (home) | `Open Graph - Home.png` | `69322470da6d6f12bcacc3d1` |
| Favicon | `Favicon.png` | `69322470da6d6f12bcacc3c8` |

**Retired from active use** (still in Webflow assets library, but no longer referenced):
- `RepsReels_Logo.png` (`693224bc137c88a8368d8da8`) — previous nav/footer mark, replaced 2026-04-20

### Custom code (Webflow Site Settings → Custom Code → Head)

The canonical file is **`./webflow_head_paste.html`** (834 lines). It contains:
- All `@font-face` declarations
- The full `:root` CSS variable set
- All component styles including hero, marquee, stats-band, voice, reels, about, contact, footer
- Responsive breakpoints (`@media` for ≤991, ≤767, ≤479)
- Marquee animation keyframes
- Skip-link + reduced-motion guards

When editing custom code in Webflow: copy this file into the Head block. Do not hand-edit in Webflow without syncing back to this file.

### Inline style overrides (documented workarounds)

A handful of properties are applied via inline `style=""` attributes on elements because Webflow's MCP style_tool silently rejects them at the `main` breakpoint. If you refactor, these need to move:

| Element | Inline style | Reason |
|---|---|---|
| Nav mark image | `height:44px; width:auto; max-width:72px; filter: drop-shadow(…brass glow…)` | `main`-breakpoint filter/size updates rejected |
| Footer mark image | `height:36px; width:auto; max-width:56px; filter: drop-shadow(…)` | Same |
| Marquee section | `background-color:#c9a24b; border-top-color:…; border-bottom-color:…` | Class `background-color` rejected on `main` |
| Stats-band numbers (x3) | `color:#6B1E1E` | Class `color` update rejected |
| Voice cornerstone | `border-left:6px solid #6B1E1E; padding-left:20px; border-top:none; padding-top:0; margin-top:24px` | Class `border-*` rejected |

An injected DOM helper (`.rr-marquee-dark-text`, aria-hidden) carries the CSS that forces `.marquee .marquee__item { color:#0b0b0c }` since class-level updates were failing.

### Hero atmosphere glow

A dedicated `.hero__atmosphere` div sits behind the crest with a stacked multi-radial `background-image`. This was added because Webflow rejected multi-gradient `background-image` values on the parent `.hero` class. The div's CSS was injected via `whtml_builder` into the head. See `webflow_head_paste.html` for the full gradient stack (desktop + small-breakpoint variant).

### Mobile behavior (≤767px)

- Nav: tagline hidden, mark scales down to fit, wordmark 16px, socials compressed
- Hero: `flex-direction: column` — crest sits above text, full-width crest up to `min(92vw, 440px)`
- Marquee: 10px vertical padding, 16px item font
- Stats: stacks vertically

### SEO package in place

- `<title>` + meta description: set in Home page settings
- Open Graph: Home OG image, title, description
- `<html lang="en">` confirmed
- All images have meaningful `alt` text
- Skip-link for keyboard nav
- Single `<h1>` per page; heading hierarchy valid

---

## What's NOT Shipped (Deferred)

These are known gaps that have been explicitly deferred. They are captured in the integration punch list (see `IG_TT_INTEGRATION.md`) or will be handled separately.

- **Live IG/TT reel embeds** — currently 6 `[ Reel embed ]` placeholder cards. Need content picks + embed code.
- **Omnisend newsletter form** — placeholder block only. Needs form embed code from Omnisend account.
- **Custom domain** — staging is on `.webflow.io`; `repsreels.com` not yet pointed.
- **Stickers / tattoo crest variant** — open request from the 2026-04-20 audit (items A–E in `archive/2026-04-20_audit/audit_2026-04-20.md`).
- **Additional pages** — homepage is the only page. Future: `/about`, `/posts/`, `/licenses` exist as Webflow page templates but are not yet wired.

---

## File Map (Live Directory)

| File | Role |
|---|---|
| `SOURCE_OF_TRUTH.md` | This doc. |
| `IG_TT_INTEGRATION.md` | Punch list for wiring in live reel embeds. |
| `README.md` | Quick orientation for anyone new to the folder. |
| `homepage_prototype.html` | **Design reference.** The approved static prototype from 2026-04-20. Webflow site matches this visual spec. |
| `homepage_copy.md` | Copy deck — every string shipped, in order. |
| `webflow_head_paste.html` | Custom CSS/JS pasted into Webflow Site Head. |
| `image_specs.md` | Image sizing / format specs for assets. |
| `reels_embed_spec.md` | How the reels section is structured and how to embed real reels. |
| `seo_package.md` | Meta + OG source copy. |
| `launch_checklist.md` | Pre-public-launch gate. |
| `fpo/` | Placeholder reel thumbnails used while embeds are being wired. |
| `archive/` | Everything historical. See `archive/README.md`. |

---

## How to Edit This Site

1. **Small text tweaks (copy only):** edit directly in Webflow Designer, then update `homepage_copy.md` to match.
2. **Style changes:** prefer editing `webflow_head_paste.html` locally, then paste the full file into Webflow Site Settings → Custom Code → Head. Publish.
3. **Adding or removing sections:** edit in Webflow Designer and update this doc + `homepage_prototype.html` the same day. Do not let them drift.
4. **Asset swaps:** upload via Webflow Designer Assets panel (MCP has no upload endpoint), then swap in the element reference.
5. **Publishing:** `data_sites_tool.publish_site` to the Webflow subdomain. Custom domain publish is a separate action when the domain is ready.

---

## Version History

| Date | Summary |
|---|---|
| 2026-04-19 | Initial build, rounds 1–7. Multiple QA passes. (See `archive/2026-04-19_build/`.) |
| 2026-04-20 (AM) | Audit flagged: too dark, logo too small, hook bland, brand mark unclear. Prototype rebuilt. Round 8 redesign prompt written. (See `archive/2026-04-20_audit/`.) |
| 2026-04-20 (PM) | Redesign applied in Webflow: crest-forward hero, brass marquee, oxblood accents, stats band, personal about copy. Mobile responsive pass: nav shrink, hero column stack, crest sized to fill. Nav + footer marks swapped to `RRbrass_alpha.png`. **Locked as source of truth.** |
