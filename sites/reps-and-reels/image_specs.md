# Reps & Reels — Image Spec Sheet
**Stream A3 deliverable** — 2026-04-19
**Rule:** No stock. Ever. Every slot is either an existing brand asset, a pull from Steve's own content, or a generated image using the prompts below.

---

## Summary of image slots

| # | Slot | Source | Asset / Prompt |
|---|---|---|---|
| 1 | Hero visual | Existing asset | `brand_mark_primary_gold.png` |
| 2 | Voice moment background (optional) | Generated | See S2 below |
| 3 | Reels grid (content) | Platform embeds | N/A |
| 4 | About / origin section | Existing asset | `illustration_set_candlelit.png` (crop) |
| 5 | Contact section accent | Existing asset | `rr_sticker.png` |
| 6 | OG / link preview image | Generated | See S6 below |
| 7 | Favicon | Existing asset | Derived from `rr_sticker.png` |
| 8 | Apple touch icon | Existing asset | Derived from `brand_mark_primary_gold.png` |

---

## S1 — Hero visual (LOCKED)

**Asset:** `/Reps and Reels/Assets/brand_mark_primary_gold.png`
**Usage:** centered behind H1, desktop at 60% opacity, mobile at 40% opacity (prevents fighting the H1 weight on small screens).
**Action item:** convert to SVG before handoff if the source file supports it. PNG at large sizes gets jagged on retina displays.
**Alt text:** "Reps & Reels brand mark — a raised fist holding a set of bagpipes, in gold on black."

---

## S2 — Voice moment background (optional, recommended)

**Purpose:** a textured backdrop behind the cornerstone line, so the "years are invisible" callout has physical weight under it.
**Style:** abstract, near-black, heavy grain, suggests old iron or stone — not overt.

**Nano Banana / Midjourney prompt:**
```
A moody, extreme close-up photograph of worn, aged cast iron surface with deep shadows, heavy grain, subtle golden highlights catching edges from a single warm side light, 16:9 aspect ratio, editorial magazine style, film grain, desaturated with warm midtone cast, no text, no logos, no people, abstract texture only, dark and atmospheric, Criterion Collection poster aesthetic, shot on medium format film, deeply dark with 80% of frame in shadow
```

**Alt text:** "" (decorative)
**Fallback:** if generation doesn't hit, use a solid #0B0B0C background with no image. The copy carries the section.

---

## S3 — Reels grid (content)

**Source:** Instagram and TikTok embeds via the site's Webflow CMS reels collection. See Stream B2 spec.
**No static images required for this slot** — the embeds ARE the content.
**Fallback state (for failed embeds):** a div with Aged Brass border, black fill, mono-typography text:
```
Reel not loading.
Full feed on @repsreels (IG + TikTok).
```

---

## S4 — About / origin section visual

**Asset:** `/Reps and Reels/Assets/illustration_set_candlelit.png`
**Usage:** crop one panel of the 4-panel set, full-bleed on desktop-left column or full-width mobile top. 40% opacity with Forge Black overlay so the type on top remains readable.
**Action item:** review the 4 panels, pick the one that reads most like "hands doing work by candlelight" — avoid the panel that features a full figure. Hands and tools are preferred (see Imagery Rule: no full faces in chrome).
**Alt text:** "" (decorative)

**If the asset doesn't work:** fall through to the prompt below.

**Backup Nano Banana / Midjourney prompt:**
```
A candlelit still life of a set of bagpipe practice chanter laid on dark wood next to an old weight plate with chalk marks, single warm candle flame source, heavy shadows, deep earthy tones with warm gold highlights, editorial documentary photography, medium format film aesthetic, heavy grain, 75% shadow 25% warm candlelight, no people, no text, atmospheric and contemplative, extreme low-key lighting
```

---

## S5 — Contact section accent

**Asset:** `/Reps and Reels/Assets/rr_sticker.png` (round emblem)
**Usage:** one static emblem at 96px, rendered to the left of or above the "Hit me." heading. Desaturated to match the section's palette if needed.
**Alt text:** "Reps & Reels round emblem."

---

## S6 — OG / link preview image (CRITICAL — this is what shows in iMessage, Slack, IG story share)

**Purpose:** when someone shares a link to repsreels.com, this is the card that renders. Has to read the brand in under a second at 1200×630 pixels.

**Style specs:**
- 1200×630 pixels (standard OG)
- Forge Black background
- Aged Brass H1 text across left two-thirds
- Brand mark (fist + pipes) on right third at 50% opacity
- IBM Plex Mono small-tagline across bottom

**Text on image:** `TWO HOBBIES NOBODY ASKED ME TO COMBINE.` (display font) + `REPSREELS.COM` (mono, bottom right).

### Option A: Design in Figma/Canva (recommended, deterministic)
Build this manually — don't leave an OG image to AI generation. Text rendering in AI models is unreliable at 12px type.
Template spec:
- Bg: `#0B0B0C`
- H1: `#C9A24B`, Big Shoulders Display 900, 84px, tracking -0.02em
- Tagline: `#E8E4DB`, IBM Plex Mono 600, 14px uppercase, tracking 0.15em
- Brand mark: existing PNG, 50% opacity, right-aligned
- Export: PNG, <200KB, dimensions 1200×630

### Option B: Generate the *background* and overlay text in Figma
Use the S2 prompt above to generate the textured backdrop, then overlay text in Figma. Cleaner than letting AI render small type.

**Alt text:** "Reps & Reels — Two hobbies nobody asked me to combine. — A creator brand at the intersection of competitive lifting and bagpiping."

---

## S7 — Favicon

**Source:** `/Reps and Reels/Assets/rr_sticker.png`
**Action item:** must read at 16px. The full emblem won't. Use the "RR" monogram portion only. Isolate and re-export at:
- 16×16 PNG
- 32×32 PNG
- 180×180 PNG (Apple touch icon)
- 512×512 PNG (manifest/PWA)
- SVG (modern browsers)

**If the monogram isn't isolable from the emblem cleanly:** generate a new simple `RR` monogram in Aged Brass on Forge Black. Keep weight heavy (Big Shoulders 900 or similar) so 16px renders as readable shapes, not mush.

---

## S8 — Apple touch icon

**Source:** `/Reps and Reels/Assets/brand_mark_primary_gold.png`, cropped to a square framing of the fist+pipes mark (not the full composition with "TRAIN · MUSIC · DISCIPLINE" text).
**Spec:** 180×180 PNG, rounded corners handled by iOS automatically, Forge Black background, brand mark centered at ~75% of frame height.

---

## Generation tools ranked

If Steve is generating new images, recommended order:

1. **Nano Banana** (Gemini 2.5 Flash Image) — best price/quality for textures and abstract compositions. Fast.
2. **Midjourney v7** — best editorial/cinematic output. Slower, subscription, but the S2 texture prompt will hit harder here.
3. **DALL-E 3 via ChatGPT** — acceptable fallback. Worst of the three for textural depth.

For OG image specifically — **don't generate, design.** Use Figma or Canva. Text rendering in AI at small sizes is where the brand voice dies.

---

## QA before handoff

- [ ] Every image tested at mobile width (375px) to confirm crops still read
- [ ] Every image optimized — AVIF + WebP + JPG fallback, via Webflow's built-in or manually with `sharp`
- [ ] OG image tested in iMessage preview, Slack unfurl, and IG story share (screenshot each)
- [ ] Favicon tested at 16px — squint at the browser tab, does it read?
- [ ] All decorative images have alt="" set explicitly (not missing — explicit empty)
- [ ] All content images have descriptive alt text under 125 characters
- [ ] No image over 200KB after compression
