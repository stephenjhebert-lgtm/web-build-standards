# Claude Code — Round 5 Clean Rebuild (Reps & Reels)
**Paste everything below into a fresh Claude Code session.**

---

Rounds 1-4 produced compounding broken state on the Webflow homepage: locked components blocking class application, duplicate `rr-*` classes, MCP `set_style` succeeding-but-not-persisting on 10 elements, partial stylesheet truncation in Head Code (resolved via DOM `<style>` element). The page is unsalvageable through patches. Round 5 deletes the homepage and rebuilds from scratch, using the one MCP behavior we know works: classes applied at element creation persist; classes applied via `set_style` to existing styleless elements don't.

**Site:** `https://reps-reels-320557.webflow.io/`
**Designer:** `https://reps-reels-320557.design.webflow.com/`

**Read first:**
1. `/Reps and Reels/web/homepage_prototype.html` — source of truth for markup AND class names. Every Webflow element you create must mirror this file's element + class structure.
2. `/Reps and Reels/web/build_log_2026-04-19_round4.md` — what's broken and why. The DOM `<style>` element on the current page is the working stylesheet — DO NOT recreate it.

---

## Critical MCP rules (do not violate)

1. **Every element you insert MUST be created with its class names included in the initial create call.** Never create an element and then attach a class with `set_style` afterward — that's the failure path that produced 10 broken bindings in round 4.
2. **All class names must already exist in the site's Styles panel** (they do — round 3 + 4 created them). When inserting an element, reference existing classes by name. Webflow MCP should accept existing class names and bind them on creation.
3. **Use lowercase BEM class names exactly as in `homepage_prototype.html`** — `hero__h1`, `voice__cornerstone`, `reels__grid`, `about__inner`, `contact__h2`, `footer__mark`, etc. Do NOT use Pascal-case duplicates (`Container`, `Nav`, `Footer`). Webflow's case-insensitive collision with these is what caused half of round 4's chaos.
4. **Do NOT use components.** Insert plain elements only. The skip-link and other elements got locked into component maps in prior rounds, blocking class binding.

---

## Step 1 — Delete the existing homepage content

1. Open the Designer at the homepage.
2. Select the `<body>` element in the Navigator.
3. Delete every direct child of `<body>` EXCEPT the DOM `<style>` element (element ID `21e355f7-3ce4-0616-0794-a38a3ebcb071`). That `<style>` carries the full 14,252-char stylesheet — keep it.
4. Verify body now contains only the `<style>` element.

**Do not touch Site Settings → Custom Code → Head Code** in this round. The DOM `<style>` element is doing the work.

Report: `[Step 1] DONE — Body cleared except <style> element. Next: Step 2.`

---

## Step 2 — Build the page top-to-bottom from prototype

For each section below, insert elements in order, with classes bound on creation. Match `homepage_prototype.html` markup exactly — same tag, same nesting, same class names.

### 2.1 Skip link
- Insert `<a>` with classes `skip-link` and href `#main` and inner text "Skip to content"

### 2.2 Nav (`<nav>`)
- Class: `nav`
- Inner div with class `container` containing inner div with class `nav__inner`
  - Left: `<a>` with class `nav__wordmark` containing `<img>` with class `nav__mark` (use existing nav logo asset) and text "REPS & REELS"
  - Right: div with class `nav__right` containing two `<a>` with class `nav__social` linking to Instagram and TikTok

### 2.3 Hero (`<section id="main">`)
- Class: `hero`
- Inner: div with class `hero__bg` (empty, decorative)
- Inner: div with class `container` containing div with class `hero__content`:
  - `<span>` with classes `hero__chip` and `mono` and text "RR · THE OVERLAP"
  - `<h1>` with classes `hero__h1` and `h1` and text "Two hobbies nobody asked me to combine."
  - `<p>` with classes `hero__subhead` and `subhead` and text "Competitive lifting. Competitive piping. Same person. The overlap is the whole thing."

### 2.4 Voice moment (`<section>`)
- Class: `voice`
- Inner: div with class `container` containing div with class `voice__inner`:
  - `<p>` with class `voice__body` and text "Not a fitness page. Not a bagpipe page. A page about the years it takes to be quietly good at anything."
  - `<p>` with class `voice__cornerstone` and text "THE YEARS ARE INVISIBLE. THE RESULT ISN'T."

### 2.5 Reels (`<section>`)
- Class: `reels`
- Inner: div with class `container`:
  - div with class `reels__header`:
    - `<span>` with classes `reels__label` and `mono` and text "ROTATING"
    - `<h2>` with class `h2` and text "What's currently rotating."
  - div with class `reels__grid` containing 6 `<a>` with class `reel`. For each `<a>`:
    - href: `#` (placeholder)
    - target: `_blank`, rel: `noopener`
    - `<span>` with class `reel__platform` text alternating: IG, TIKTOK, IG, TIKTOK, IG, TIKTOK
    - div with class `reel__placeholder` containing `<img>` with src bound to the corresponding reel asset (asset IDs from round 4 build log: `69e55965b626c4a0230d079b` through `69e55965f63ac5ac24e2650f`, in order). alt text: "Reel placeholder"
    - `<p>` with class `reel__caption` and text "Placeholder caption — real captions arrive once CMS is enabled."
  - `<p>` with class `reels__fallback-note` containing the literal HTML: `Recent. The rest is on IG and TikTok — <a href="https://instagram.com/repsreels" target="_blank" rel="noopener">@repsreels</a> on both.`

### 2.6 About (`<section>`)
- Class: `about`
- Inner: div with class `container` containing div with class `about__inner`:
  - `<span>` with classes `about__label` and `mono` and text "ABOUT"
  - `<h2>` with classes `about__heading` and `h2` and text "About."
  - div with class `about__body` containing two `<p>`:
    - "I'm Steve. I lift heavy and I play the great Highland bagpipe. Most days that means a 5am gym session and a competition kilt by Saturday."
    - "If both worlds feel like work, you're doing it right."

### 2.7 Contact (`<section>`)
- Class: `contact`
- Inner: div with class `container`:
  - `<span>` with classes `contact__label` and `mono` and text "CONTACT"
  - `<h2>` with classes `contact__h2` and `h2` and text "Hit me."
  - `<p>` with class `contact__body` containing the literal HTML: `Email: <a class="contact__email-link" href="mailto:steve@repsreels.com">steve@repsreels.com</a>`
  - div with class `contact__form-block`:
    - `<p>` with class `contact__form-copy` and text "Occasional email. Not a newsletter exactly."
    - div with class `contact__form-placeholder` and text "Omnisend form embeds here."

### 2.8 Footer (`<footer>`)
- Class: `footer`
- Inner: div with class `container`:
  - div with class `footer__top`:
    - div with class `footer__wordmark` containing `<img>` with class `footer__mark` (use existing footer mark asset)
    - `<p>` with class `footer__tagline` and text "QUIET WORK WINS."
  - div with class `footer__row`:
    - div with class `footer__social` containing two `<a>` linking to IG and TikTok
    - `<span>` with class `footer__email` and text "STEVE@REPSREELS.COM"
  - `<p>` with class `footer__copy` and text "© 2026 REPS & REELS. ALL RIGHTS RESERVED."

Report each section as you complete it: `[Step 2.X] DONE — <section> built. Next: 2.X+1.`

---

## Step 3 — Publish + verify

Publish to staging. Run on `https://reps-reels-320557.webflow.io/`:

```js
({
  styleBlocks: [...document.querySelectorAll('style')].map(s => s.innerHTML.length),
  maxBlock: Math.max(...[...document.querySelectorAll('style')].map(s => s.innerHTML.length)),
  skipLink: document.querySelectorAll('.skip-link').length,
  nav: document.querySelectorAll('.nav').length,
  navInner: document.querySelectorAll('.nav__inner').length,
  hero: document.querySelectorAll('.hero').length,
  heroH1: document.querySelectorAll('.hero__h1').length,
  voice: document.querySelectorAll('.voice').length,
  voiceCornerstone: document.querySelectorAll('.voice__cornerstone').length,
  reels: document.querySelectorAll('.reels').length,
  reelsGrid: document.querySelectorAll('.reels__grid').length,
  reel: document.querySelectorAll('.reel').length,
  reelPlatform: document.querySelectorAll('.reel__platform').length,
  reelImgsBroken: [...document.querySelectorAll('.reel img')].filter(i => !i.complete || i.naturalWidth === 0).length,
  about: document.querySelectorAll('.about').length,
  aboutInner: document.querySelectorAll('.about__inner').length,
  contact: document.querySelectorAll('.contact').length,
  contactH2: document.querySelectorAll('.contact__h2').length,
  footer: document.querySelectorAll('.footer').length,
  footerMark: document.querySelectorAll('.footer__mark').length,
  container: document.querySelectorAll('.container').length
})
```

Pass conditions:
- `maxBlock` ≥ 14,000 (the DOM `<style>` element)
- `skipLink`, `nav`, `hero`, `heroH1`, `voice`, `voiceCornerstone`, `reels`, `reelsGrid`, `about`, `aboutInner`, `contact`, `contactH2`, `footer`, `footerMark` all = 1
- `reel`, `reelPlatform` = 6
- `reelImgsBroken` = 0
- `container` = 7

If ANY count is 0 (or wrong), the class wasn't bound on creation. Report which element + which class failed. Do not retry — flag for Steve.

Report: `[Step 3] DONE — Final verification: {...}`

---

## Step 4 — Build log + STOP

Write to `/Reps and Reels/web/build_log_2026-04-19_round5.md`:
- Confirmation body was cleared (with `<style>` element preserved)
- Each section built with the classes that landed
- Final verification snippet output
- Any element whose class binding failed

Do NOT publish to production. Staging only.

---

## Hard stops
- ❌ No production publish
- ❌ No DNS changes
- ❌ No CMS attempt
- ❌ No components — plain elements only
- ❌ No Pascal-case classes (`Container`, `Nav`, `Footer`) — lowercase BEM only
- ❌ No `set_style` after element creation — bind classes at insert time only
- ❌ No edits to the existing DOM `<style>` element
- ❌ No retries on failed class binding — flag instead

## Success criteria
Round 5 is complete when Step 3's verification snippet returns all target values on first publish. If any class fails to bind even at creation time, report immediately — that's a deeper Webflow MCP issue and we'll route differently.

Start with Step 1.
