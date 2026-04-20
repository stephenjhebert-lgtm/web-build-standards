# Reps & Reels — Design Direction
**Stream A2 deliverable** — 2026-04-19
**Source of truth pulled from:** `/Brand/brand_overview.md` (iron-and-flowers, 75/25 ratio), `/Assets/brand_mark_primary_gold.png` (primary palette extraction), brief's "bold, loud, high-contrast" directive.
**Skill applied:** `repsreels-design`.

---

## Design thesis (one sentence)

A single visual space that holds both barbell weight and pipe-band ceremony — rugged first, warm second, no split-screen, no costume, no toggle. The collision is in the overlap of materials (iron, brass, gold, tartan shadow), not in a compartmentalized layout.

---

## What the brief got right and what it got risky

**Got right:** Bold, loud, high-contrast. Magazine/poster energy over SaaS landing page. Editorial imagery. Mobile-first.

**Risky:** "Bold and loud" is the fastest route to generic fitness brand or generic pipe band poster. The design has to be loud the way a Criterion movie poster is loud — weight, restraint, confident typography, one image doing the work — not the way a supplement landing page is loud.

**Guardrail:** If a section can be recolored to a protein brand without anyone noticing, it's not the right design.

---

## 1. Palette

Pulled from the existing `brand_mark_primary_gold.png`, then tightened.

| Role | Name | Hex | Notes |
|---|---|---|---|
| Background (primary) | **Forge Black** | `#0B0B0C` | Near-black, slight warm cast. Not pure `#000`. Holds more depth in photography. |
| Foreground (primary) | **Aged Brass** | `#C9A24B` | The gold from the brand mark. Warm, not neon. Matches aged bagpipe drone tops and old iron. |
| Background (secondary) | **Bone Paper** | `#F1ECE1` | Warm off-white. For light-mode sections if needed. Not pure white — reads as parchment/sheet music, not SaaS. |
| Accent 1 | **Oxblood** | `#6B1E1E` | Deep red. Used sparingly — pipe band colors, weight plate rubber, one hover state. Never dominant. |
| Accent 2 | **Iron Grey** | `#3A3A3D` | Card backgrounds, dividers, subdued typography. |
| Text on dark | **Paper** | `#E8E4DB` | Body copy color on Forge Black. Softer than pure white. |
| Success / signal | *(none)* | — | Intentional. Site has no form validation states. If Omnisend form needs them, let Omnisend handle it. |

**75/25 ratio enforcement:** Forge Black + Iron Grey + Paper text = the 75% rugged. Aged Brass + Bone Paper + Oxblood = the 25% warmth. Design review rule: if any section reads more than 40% warm tones, cut it back.

**Kill-list for palette drift:** no neon yellow, no gym-pink accent, no tartan-pattern background fills, no gradient overlays, no drop shadows with colored tint.

---

## 2. Typography

Current site uses **Fustat** (Google Fonts). Fustat is a clean neutral sans with no opinion — appropriate for SaaS, wrong for this brand. Proposing a two-font system.

### Display (H1, section headers, footer wordmark)

**Primary choice:** **Druk Wide** (Commercial Type) — condensed, ultra-heavy sans. Magazine cover weight. Brand partnership-appropriate. Handles "Two hobbies nobody asked me to combine." without feeling thin.

**Free alternative:** **Big Shoulders Display** (Google Fonts, free). Similar condensed heavy sans. 90% of the impact at 0% of the cost. **Recommend starting here** — upgrade to Druk only if a partnership budget opens up.

### Body (paragraphs, captions, metadata)

**Primary choice:** Keep **Fustat** or swap to **Inter**. Fustat is already on the site, so staying with it is zero-friction. Inter is slightly more refined if we want an excuse to change. **Recommend:** keep Fustat for body only. One less decision, one less font file to load.

### Accent (pull quotes, tagline callouts — "QUIET WORK WINS.", cornerstone)

**Choice:** **JetBrains Mono** or **IBM Plex Mono** — mono-spaced for the ceremonial-plaque energy the brand leans into on the QUIET WORK WINS graphics. Used only for the cornerstone line and taglines. Never body copy.

### Type scale (mobile → desktop)

| Level | Mobile | Desktop | Font |
|---|---|---|---|
| H1 (hero) | 48px / 1.0 leading | 112px / 0.95 | Big Shoulders Display 900 |
| H2 (section) | 32px / 1.1 | 56px / 1.05 | Big Shoulders Display 800 |
| Subhead | 18px / 1.3 | 22px / 1.35 | Fustat 500 |
| Body | 16px / 1.5 | 18px / 1.55 | Fustat 400 |
| Small / caption | 13px / 1.4 | 14px / 1.4 | Fustat 400 |
| Accent (taglines) | 14px / 1.0 tracking 0.15em | 16px / 1.0 tracking 0.15em | IBM Plex Mono 600 uppercase |

**Mobile note:** H1 at 48px is deliberately massive on mobile. Phone screens don't feel loud at 36px. 48px with tight leading is the magazine-cover effect.

---

## 3. Imagery style

No stock. Ever. Three image categories allowed on the site.

### Category 1: Existing brand assets (preferred for hero and brand touchpoints)
- `brand_mark_primary_gold.png` — hero visual (locked)
- `manifesto_quiet_work_wins.png` — potential section divider
- `illustration_set_candlelit.png` — about/origin section background option
- `rr_sticker.png` — favicon source

### Category 2: Steve's own content (photos/stills pulled from IG/TikTok posts)
- Only photos where the imagery has editorial density — shadows, close-ups, texture.
- No selfies. No posed gym mirror shots. No pipe band group photos.
- Good pulls: chanter close-up on a gym bench, weight plate on wood floor, hands on a grip (either barbell or chanter).

### Category 3: Generated imagery (Nano Banana / Midjourney) — for specific missing slots only
Use case: OG image, any decorative background we can't pull from existing assets. Full prompts live in A3.

### Imagery rules

- **Black-and-white or duotone only** for generated and cropped imagery. Color imagery only where the existing gold/brass is part of the subject (the brand mark, a chanter, a brass plate). This prevents "gym stock photo" energy.
- **Heavy grain/noise layer** on every image — 3-8% film grain. Softens the AI-feel and anchors the editorial/poster aesthetic.
- **Crops lean extreme.** Close-up knuckles, chanter reed, boot on deck. No wide establishing shots.
- **No people's full faces in decorative imagery.** Hands, silhouettes, crops. The brand mark handles the identity work. Steve's face lives in the reels, not in the page chrome.

### Alt text policy

- Every decorative image: alt="". (Empty alt, marks as decorative, screen readers skip it.)
- Every content image: real description, no SEO stuffing. ("Close-up of hands on a barbell knurl, shot from above.")
- Reel embeds: alt text falls through to the platform's embed. Our fallback div describes what the block is: "Recent Instagram reel — visit @repsreels if this embed fails to load."

---

## 4. Motion principles

Used sparingly. Each one has to earn its frame budget.

### Allowed motion

1. **Hero entrance (one-time)** — H1 letters stagger in from a 4px offset over 600ms. No fade. Feels like weight plates clanging into place.
2. **Scroll-linked reveal on voice moment** — cornerstone line ("The years are invisible. The result isn't.") fades up from 20% opacity as it enters viewport. 800ms.
3. **Hover on email link** — underline scrubs in left-to-right over 200ms. That's it. No color change, no arrow animation.
4. **Hover on social icons** — icon fills from outline to solid over 150ms. Matches the brass color on hover.

### Banned motion

- Parallax scrolling
- Floating CTAs
- Auto-playing background video
- Typewriter effects
- Counter animations ("0 to 10,000 followers!")
- Anything that bounces, springs, or wobbles
- Lottie animations that feel like stickers

### Performance gate

Total JS motion budget: <15KB. If Webflow's interaction panel can't do it under that budget, it doesn't ship. The site has to feel fast even with a dozen embeds below the fold.

---

## 5. Layout and spacing

### Grid

- Desktop: 12-column, 1280px max-width container, 80px gutters.
- Mobile: single column, 24px edge padding.
- No side-scrolling sections. No horizontal carousels.

### Section rhythm

Target rhythm down the page: **dense → breathe → dense → breathe.**

| Section | Density | Vertical padding (desktop / mobile) |
|---|---|---|
| Hero | Dense (full viewport) | 100vh / 100vh |
| Voice moment | Breathe | 160px / 80px |
| Reels grid | Dense | 120px / 60px |
| About / origin | Breathe | 160px / 80px |
| Contact | Medium | 120px / 60px |
| Footer | Compact | 80px / 48px |

**Why:** Brief says the design should match the content energy. The content energy on IG/TikTok is bursts of intensity with quiet in between. The page should breathe the same way.

---

## 6. Mobile behavior (critical — 70%+ of traffic)

- **Hero:** full viewport height. H1 at 48px. Subhead below at 18px. Brand mark positioned as background element at 40% opacity, centered, blur-adjacent. No scroll prompt chevron — if they don't scroll, they weren't going to.
- **Voice moment:** stack vertically, cornerstone line gets its own row at 24px bold mono.
- **Reels grid:** 2-column at >480px, single-column below. Each embed gets 16:9 or 9:16 aspect container (based on platform), lazy-loaded with a dark placeholder.
- **About / origin:** single paragraph column, max 32ch for readability.
- **Contact:** mailto link is button-sized on mobile (minimum 48px tap target). Omnisend form below, collapsed to one-field width.
- **Footer:** stacked, social icons 48px tappable.

### Performance budget (mobile)

- Lighthouse Performance: **≥85** target, 90+ stretch
- LCP (largest contentful paint): <2.5s on 4G
- CLS (cumulative layout shift): <0.1 (reserve space for every embed slot with aspect-ratio CSS)
- Font loading: `font-display: swap`, preload Big Shoulders Display 900 (H1 weight) only

---

## 7. Dark mode

**Decision: no dark/light toggle.** The current site has one. It's adding complexity for no audience benefit.

- The brand lives in **dark primary.** Forge Black background, Aged Brass foreground. That's not a mode — that's the site.
- Only the About / origin section uses Bone Paper as a background break. That's the "breathe" moment. Not a mode toggle, just a design choice.

---

## 8. Accessibility baseline (WCAG AA minimum)

- Color contrast: Paper (#E8E4DB) on Forge Black (#0B0B0C) = 15.3:1 (passes AAA body).
- Aged Brass (#C9A24B) on Forge Black = 9.6:1 (passes AAA large, AA body).
- **Do not put Aged Brass on Bone Paper** — contrast drops to 2.8:1 (fails).
- All interactive elements: 48px minimum tap target on mobile.
- Focus states: 2px Aged Brass outline, 2px offset. No `outline: none;` without replacement.
- Semantic HTML: one `<h1>`, `<nav>` for social links, `<main>`, `<footer>`. Skip link at top.
- Reduced motion: all motion wrapped in `@media (prefers-reduced-motion: no-preference)`.

---

## 9. What this design is NOT

Logging the failure modes so we can catch drift.

- Not a supplement landing page
- Not a pipe band tour site
- Not a personal blog
- Not a portfolio grid
- Not a Medium article
- Not a Bento-box "tools I use" creator page
- Not a Stripe-style SaaS page with color gradients and iconography

---

## 10. Handoff notes for Stream B (Webflow build)

- Font loading strategy: Google Fonts self-hosted if possible (Webflow supports this in newer projects). Otherwise standard Google Fonts embed. Preload H1 weight only.
- All imagery served as AVIF with WebP fallback and JPG legacy. Webflow's native image optimization handles this.
- Brand mark (hero) is a PNG — convert to SVG if possible before handoff for crisp scaling. Flag for A3 image spec.
- Dark mode toggle logic on current site: strip entirely. Simpler DOM, lighter JS.
- Existing FAQ section on live site: not in MVP scope per brief. Remove entirely. Brief says 6-section homepage + maybe one sub-page.

---

## Questions for Steve (minor — answer when convenient)

1. **Druk Wide vs. Big Shoulders Display** — recommend Big Shoulders (free, 90% as good). Confirm.
2. **Monospace accent font** — IBM Plex Mono vs. JetBrains Mono — personal preference, either works. Default to IBM Plex.
3. **Dark mode toggle removal** — brief says no. Current site has one. Just confirming the removal is fine.

Defaulting to the recommended path on all three unless you flag otherwise.
