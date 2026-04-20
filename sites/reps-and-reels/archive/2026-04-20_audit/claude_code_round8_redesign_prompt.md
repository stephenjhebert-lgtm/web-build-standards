# Round 8 — Approved Redesign Push to Staging
**Date:** 2026-04-20
**Source of truth:** `web/homepage_prototype.html` (rewritten 2026-04-20)
**Audit & rationale:** `web/audit_2026-04-20.md`
**Status:** Steve approved direction. Push to Webflow staging.

---

## What changed vs round 7

This is a structural redesign, not a tweak. Treat the prototype as ground truth and rebuild the Webflow page from it.

### New sections / structural changes
1. **Nav:** masthead restructured — logo 28→44/52px, stacked wordmark + brass tagline "Quiet Work Wins."
2. **Hero:** two-column on desktop (left = text, right = brand crest at full opacity), single column on mobile (crest first, text below). Brass radial gradient background. Headline rewritten: "Iron in one hand. Pipes in the other."
3. **NEW — Marquee strip** between hero and Premise: brass-on-black, 38s infinite scroll, brand vocabulary
4. **NEW — Section chapter labels** (`01 / The Premise`, `02 / The Feed`, `03 / Origin`, `04 / Contact`) with mono brass numerals + thin gradient rule
5. **Premise section** flipped to bone-paper background (was dark)
6. **NEW — Stats block** in Origin section: 20+ / 5 / 3 with oxblood numerals
7. **Footer:** restructured with patch logo + brand name + tagline lockup

### Webflow-specific tasks
- [ ] Site Settings → Custom Code → Head: replace existing CSS paste with the new `<style>` block from `homepage_prototype.html` (lines 57–609). May need split into 2 blocks again per Webflow 14KB limit.
- [ ] Page DOM: rebuild sections in order — Nav, Hero, Marquee, Premise, Feed, Origin, Contact, Footer (the existing Webflow page DOM doesn't have a marquee or numbered chapter labels — these are net-new components).
- [ ] Marquee: must use CSS `animation: scroll-x 38s linear infinite` — Webflow Interactions can't do infinite linear smoothly. Plain CSS is required.
- [ ] Reels grid: keep 6 placeholder slots, 9:16 aspect, 2-col mobile / 3-col desktop.
- [ ] About / Origin: add `.about__stats` row with 3 stats.
- [ ] Confirm fonts loaded in Webflow Project Settings: Big Shoulders Display (800, 900), Fustat (400, 500, 600), IBM Plex Mono (500, 600).

### QA checklist for round-8 post-paste
- [ ] Hero crest renders at full opacity, not 25%, no overlap with H1
- [ ] Top-left logo is visibly large (≥44px on phone)
- [ ] Brass marquee scrolls horizontally without jank
- [ ] Premise + Origin sections have bone-paper background
- [ ] Section chapter labels (01–04) display with brass number
- [ ] Stats block in Origin is 2-col mobile / 3-col desktop
- [ ] No CLS — reels grid has aspect-ratio reservation

---

## Punch list (priority order, what's next)

### P0 — blockers for true production launch
1. **Replace 6 FPO reel thumbnails** with real stills from IG/TikTok. Specs in §A below.
2. **Generate `og-image.png`** — currently referenced in `<meta>` tags but file missing → broken share previews. Specs in §C below.
3. **Generate favicon set** at proper resolutions. Specs in §D below.

### P1 — brand-strengthening (Steve's specifically called-out asks)
4. **Re-spec the round patch / "sticker" logo** for nav use. Specs in §F below.
5. **Re-render the brand crest** (the "yellow t-shirt design") with transparent background so it sits cleanly on any color. Specs in §G below.
6. **Optional: SVG version of the crest** for retina sharpness. Specs in §B below.

### P2 — content / functional
7. Wire Omnisend embed in Contact section (currently dashed-line placeholder).
8. Confirm `repsreels.com` canonical resolves correctly post-push.
9. Lighthouse audit — target ≥90 mobile performance after image optimization.

### P3 — nice-to-haves
10. Editorial portrait photo for About section right column on desktop. Specs in §E below.
11. Convert Brass marquee phrases to a CMS collection so you can edit without touching code.

---

# Exact image specs

These are what you'd hand to a designer or paste into a Midjourney / Nano Banana prompt.

---

## §A — Reel thumbnails (P0)
**Used at:** `.reel__media` slots in The Feed section (6 total)

| Spec | Value |
|---|---|
| Aspect ratio | 9:16 portrait |
| Dimensions | 1080 × 1920 (min 720 × 1280) |
| Format | JPG (or AVIF/WebP if Webflow auto-converts) |
| File size | <120KB after Webflow optimization |
| Color | Color OK if subject contains gold/brass naturally; otherwise duotone (forge-black + bone) preferred |
| Treatment | Subtle film grain (3–8%); slight desaturation (-10–15%); brass-tinted highlights acceptable |
| Subject rules | No full faces. Hands on barbell, chanter close-up, plates on floor, silhouettes, kitchen-counter chanter, gym-bag-with-pipe-case, etc. |
| Crop philosophy | Lean extreme — close-up knurl, single reed, single plate edge. No wide establishing shots. |

---

## §B — Brand crest as SVG (P1, optional)
**Used at:** `.hero__crest img`, `<link rel="apple-touch-icon">`

| Spec | Value |
|---|---|
| Format | SVG, optimized via SVGO (≤30KB) |
| ViewBox | 1024 × 1024 (square) |
| Background | **Transparent** (no fill behind the artwork) |
| Color palette | Aged Brass `#C9A24B` for fills/strokes; off-white `#F1ECE1` for any inner highlights; no shadows baked in |
| Style | Trace existing `brand_mark_primary_gold.png` as cleaner vector; preserve the crest composition (REPS & REELS arch, fist + plate + bagpipes, QUIET WORK WINS banner, TRAIN · MUSIC · DISCIPLINE underline) |
| Stroke weight | Heavy enough to read at 64px favicon scale; no hairline detail |

---

## §C — OG / Twitter share image (P0)
**Used at:** `<meta property="og:image">` and `<meta name="twitter:image">`

| Spec | Value |
|---|---|
| Filename | `og-image.png` |
| Dimensions | 1200 × 630 px exact |
| Format | PNG, <300KB |
| Layout | Crest at left (~480px square, with brass radial glow), headline at right |
| Headline | "IRON IN ONE HAND. / PIPES IN THE OTHER." set in Big Shoulders Display 900 (or rasterized equivalent), bone-paper color, "PIPES" in brass |
| Subhead | "@repsreels — repsreels.com" in IBM Plex Mono 600, brass, 18px, bottom-right |
| Background | Same warm-black radial as hero (`#110D07` → `#0B0B0C`) with 5% grain overlay |
| Safe area | Keep all text within central 1080 × 540 area (Twitter card crop) |

---

## §D — Favicon set (P0)
**Used at:** `<link rel="icon">` and `<link rel="apple-touch-icon">`

| File | Size | Notes |
|---|---|---|
| `favicon.ico` | 16, 32, 48 (multi-res) | Use simplified mark — bagpipes silhouette only or RR monogram. The full crest is too detailed for 16px. |
| `favicon-32.png` | 32 × 32 | Same simplified mark |
| `favicon-192.png` | 192 × 192 | Round patch version OK at this size |
| `favicon-512.png` | 512 × 512 | Round patch version |
| `apple-touch-icon.png` | 180 × 180 | Round patch on solid forge-black, brass mark centered, **no transparency** (iOS adds rounded corners) |

**Source recommendation:** Re-export `logo_round_patch_bw.png` as a clean black-on-white version sized for 16px legibility. The `rr_sticker.png` you're using now works at 44px in the nav but blurs at favicon scale.

---

## §E — Editorial portrait (P3, optional)
**Used at:** new right column of About section, desktop only (`@media min-width: 1024px`)

| Spec | Value |
|---|---|
| Aspect ratio | 4:5 portrait |
| Dimensions | 1200 × 1500 px |
| Format | JPG (or AVIF) |
| Subject | Chanter laid across a kettlebell handle, OR hands wrapping a barbell with a bagpipe drone visible at frame edge |
| Treatment | Black-and-white or duotone (forge-black + bone-paper); 5% grain; deep shadow side, single warm key light |
| Where | Right column of `.about` on desktop ≥1024px; hidden on tablet/mobile to preserve LCP |

---

## §F — New nav "sticker" logo (P1) ⭐ Steve called this out

**Problem with current `rr_sticker.png`:**
- Designed for sticker output (round patch with `repsreels.com` text inside)
- The inner "REPS / REELS" text becomes muddy at 44–52px nav size
- The black background is opaque, so it always reads as a solid black tile against the page rather than feeling integrated
- The dot-grid texture doesn't survive scaling down

**What you actually need for the nav slot:**
A simplified mark — strong silhouette, no inner type, transparent background.

| Spec | Value |
|---|---|
| Filename | `rr_mark_nav.svg` (also export `.png` @ 96 × 96 fallback) |
| Format | SVG primary; PNG @ 2x = 96 × 96 fallback at 144 DPI |
| Background | **Fully transparent** |
| Composition | Just the bagpipes-with-fist mark (no crest border, no "REPS & REELS" arch text, no "repsreels.com" footer text) |
| Color variants needed | (1) Brass `#C9A24B` on transparent, (2) Bone `#F1ECE1` on transparent — for use on dark/light backgrounds respectively |
| Stroke | Heavy enough to read at 32px (mobile dense layouts) |
| Padding | Built-in 8% padding inside the viewBox so it doesn't touch edges when used as a 44px nav button |
| Optical sizing | Test legibility at 24px (footer), 44px (mobile nav), 64px (desktop footer), 96px (apple-touch). The mark must hold up at 24px — that's the gating size. |

**Reference for the artist:** the existing `logo_round_patch_bw.png` is closer to right (it's just the mark, no extra type) — but reverse it to brass-on-transparent and drop the outer circular border so it's a free-standing graphic mark, not a patch.

---

## §G — Re-rendered brand crest WITHOUT built-in black background (P1) ⭐ Steve called this out

**Problem with current `brand_mark_primary_gold.png`:**
- The artwork has a **baked-in black square background with a dot-pattern texture**
- This means it always shows up as a black tile — fine on the dark hero but it conflicts the moment you place it on bone-paper, brass, or any other color
- The black background also fights the page's own warm-black radial in the hero (you can see the tile edge if you look closely)

**What you need:**
The same artwork, transparent background, ready to drop on any color.

| Spec | Value |
|---|---|
| Filename | `brand_crest_primary.png` (and `brand_crest_primary.svg` if going vector) |
| Format | PNG with full alpha transparency, 2048 × 2048 master; export 1024 × 1024 web; OR SVG (preferred — see §B) |
| Background | **Fully transparent.** No black tile, no dot pattern background, no border, no rounded corners. Just the artwork on alpha. |
| Composition (preserve from original) | (1) "REPS & REELS" banner arched across top, (2) Central fist gripping bagpipes with weight plate behind, (3) Inner ivy/laurel circular border, (4) "QUIET WORK WINS." banner across bottom, (5) "TRAIN · MUSIC · DISCIPLINE" underline |
| Color palette | All fills/strokes in Aged Brass `#C9A24B` with secondary `#9E7C36` for shadow/depth. Highlights in bone `#F1ECE1`. No pure white, no pure black. |
| Internal shading/grain | Keep the etched/aged texture INSIDE the artwork (on the bagpipes, fist, plate) — that's brand. Just remove the SOLID BLACK BACKGROUND TILE behind it all. |
| Variants to deliver | (1) Brass-on-transparent (primary), (2) Bone-on-transparent (for use on dark surfaces if you ever want light artwork on dark bg), (3) Single-color black-on-transparent (for use on bone-paper or merch print proofs) |
| Aspect | Square 1:1, with the crest filling ~90% of frame and 5% transparent padding on each side |

**Why this matters:** with a transparent version, the hero radial brass glow can shine THROUGH the artwork instead of stopping at the black tile edge. You'll also be able to drop the crest on the bone-paper Origin section as a subtle background watermark (currently we use the round patch logo there as a workaround — the full crest would be more on-brand if it had no black tile).

**Prompt language for re-render** (if generating via Midjourney / Nano Banana):

> Vintage athletic department crest, single color aged brass on a fully transparent background, no background tile, no border. Composition: "REPS & REELS" arched banner at top, central muscular fist gripping crossed Highland bagpipes with a weight plate behind the fist, ivy circle frame, "QUIET WORK WINS." banner at bottom, "TRAIN · MUSIC · DISCIPLINE" underneath. Style: distressed screenprint, etched lines, subtle grain INSIDE the artwork only, no halftone background. Output: 2048x2048 PNG with alpha channel.

---

## §H — Photographic asset rules (apply to all imagery)

These hold across §A and §E:
- **No stock photography** ever
- **No full faces** in decorative imagery (Steve's face lives in the reels, not the chrome)
- **Heavy crops:** close-up knuckles, chanter reed, boot edge, single plate
- **Color philosophy:** B&W or duotone unless the gold/brass is part of the subject
- **Grain layer:** 3–8% film grain on every image
- **Compression:** target <120KB per image, AVIF preferred with WebP+JPG fallbacks (Webflow handles this)

---

## Open questions for you

1. Should the round-8 push happen via Webflow MCP automation, or do you want the paste-and-confirm flow you've used through round 7?
2. For §F (sticker logo): keep the round patch border and just rework the inside, or fully drop the patch frame and ship a free-standing mark? (My recommendation: free-standing mark for nav use, keep the patch version for actual stickers/merch.)
3. For §G (re-rendered crest): are you re-commissioning the artwork, or just having someone alpha-mask the black tile out of the existing PNG? (Alpha-mask is faster but may leave halftone artifacts. Re-commission gets you a clean SVG too — I'd push for that.)

Defaulting to "Option A — paste-and-confirm" for the staging push unless you say otherwise.

---

# Round 8.1 — Asset cleanup + paste-ready status (2026-04-20 update)

Steve uploaded 4 new crest renders with checker-pattern backgrounds:
- `bonelogo.png`, `brasslogo.png`, `blacklogo.png` — full crest, three color variants
- `RRbrass.png` — round patch / nav-icon variant

**Background knockout completed locally** via three-stage chroma-key pipeline:
1. Hue/saturation envelope to identify the two-tone grey checker bg (~145 / ~200)
2. Luminance bracketing to spare brass artwork + dark interior detail
3. Morphological erode-dilate (radius 8) cleanup pass to kill residual checker halos

Cleaned outputs in `Assets/`:
- `Assets/bonelogo_alpha.png`
- `Assets/brasslogo_alpha.png`  ← used as hero crest
- `Assets/blacklogo_alpha.png`
- `Assets/RRbrass_alpha.png`    ← used as nav mark + favicon + footer mark

**Prototype + asset references already wired:** `web/homepage_prototype.html` updated to point at all four `*_alpha.png` files (favicon, apple-touch-icon, nav, hero crest, voice bg, footer mark).

**Hero grid fix:** desktop crest column was being squeezed by `1fr 1.15fr` because hero copy's min-content reserved ~680px. Switched to `minmax(0, 1fr) minmax(0, 1.25fr)` — crest now renders at 596px desktop / 380px mobile as intended.

## Webflow paste files (regenerated 2026-04-20)

| File | Size | Use |
|---|---|---|
| `web/webflow_head_paste_split.html` | 26.5KB total (Block A 13.3KB, Block B 10.9KB) | **Use this for Webflow.** Two `<style>` blocks pasted sequentially in Site Settings → Custom Code → Head |
| `web/webflow_head_paste.html` | 26.4KB single block | Reference only — exceeds Webflow's 14KB head limit |

## Asset upload checklist for Webflow Asset Manager (do this BEFORE pasting head code)

Upload these PNGs to Webflow's asset library, then update each Webflow `<img>` `src` to the Webflow CDN URL:

- [ ] Upload `Assets/rr_nobg.png` ⭐ **NEW — supplied with clean transparent bg by Steve** → use for: hero crest, apple-touch-icon
- [ ] Upload `Assets/RRbrass_alpha.png` → use for: favicon `<link>`, nav `<img>`, footer mark
- [ ] Upload `Assets/blacklogo_alpha.png` → use for: voice section background watermark (low opacity)
- [ ] Upload `Assets/bonelogo_alpha.png` → reserved for future light-bg use (not yet wired)
- [ ] (skip) `Assets/brasslogo_alpha.png` — superseded by `rr_nobg.png`, do not upload

## Known residual issues (acceptable for staging push)

1. **§F nav sticker logo still uses `RRbrass_alpha.png`** — not the dedicated free-standing mark called for in §F. Acceptable for now; commission the proper SVG mark per §F when budget allows.
2. **og-image.png still missing** — broken share previews. Generate per §C before any social/PR push.
3. **Hero crest superseded 2026-04-20 PM** — Steve delivered a clean `rr_nobg.png` (RGBA, transparent bg, diamond-plate texture intentionally preserved inside artwork). `brasslogo_alpha.png` is no longer referenced. Keep as backup only.

## Recommended push order

1. Upload 4 alpha assets to Webflow Asset Manager
2. Replace head code via Block A + Block B paste (split file)
3. Update all `<img src>` in the page DOM to point at Webflow CDN URLs for the new alpha assets
4. Add the marquee section (net-new component) per CSS classes `.marquee` / `.marquee__track` / `.marquee__item`
5. Add numbered chapter labels (`01 / The Premise`, etc.) per `.section-label` markup
6. Add the `.about__stats` row (3 stats) in the Origin section
7. Publish to staging subdomain, run QA checklist above
8. Lighthouse audit, fix any P0/P1 regressions
9. Promote to production

