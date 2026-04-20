# Claude Code — Round 4 Fix Prompt (Reps & Reels)
**Paste everything below this line into a fresh Claude Code session.**

---

Rounds 1–3 kept failing at the same two points: Webflow's Site Settings → Custom Code → Head field truncates pastes at ~13-14KB (confirmed across three rounds — single-block paste landed at 14,252 chars, split-block paste landed at 13,309 chars total), and some class bindings don't reach the published DOM.

Round 4 switches strategy to route around both failures. Follow this exactly — do not improvise.

**Read these first:**
1. `/Reps and Reels/web/build_log_2026-04-19_round3.md` — prior round's diagnosis
2. `/Reps and Reels/web/webflow_head_paste.html` — the stylesheet (source of truth for CSS)
3. `/Reps and Reels/web/homepage_prototype.html` — source of truth for class names and markup

**Staging URL:** `https://reps-reels-320557.webflow.io/`
**Designer URL:** `https://reps-reels-320557.design.webflow.com/`

---

## Strategy change for this round

- **Stop using Site Settings → Head Code for the stylesheet.** It truncates. Abandon it.
- Instead, inject the full stylesheet via an **Embed element** dropped into the page body. The Embed component has no paste size limit — the full 22KB stylesheet will land intact.
- **Classes that MCP `set_style` reports as "success" but don't reach the DOM after publish** — rebind them by creating + applying new Webflow Designer styles (not raw class attrs), then publish and re-verify. If they still don't persist after one rebind attempt, STOP and flag the element by Webflow element ID for manual user handling. Don't burn cycles on more than one retry.

---

## Step 1 — Clear the failed Head Code paste

1. Open Site Settings → Custom Code → Head Code via MCP.
2. Delete the entire content of that field. Leave it empty. Save.
3. Verify: `document.head.querySelectorAll('style').length` on staging should drop from 5 to 3 (Webflow's native runtime blocks remain).

Report: `[Step 1] DONE — Head Code cleared. Next: Step 2.`

---

## Step 2 — Inject the full stylesheet via Embed element

1. In Designer, navigate to the homepage.
2. Add a new **Embed** component at the very top of the page body (before the nav). Use the MCP element creation API to insert an embed element with:
   - Type: `embed` (or Webflow's equivalent custom-code-block type)
   - Content: the **literal, complete** contents of `/Reps and Reels/web/webflow_head_paste.html`, including the `<!-- -->` header comment and both opening `<style>` and closing `</style>` tags. All ~22,000 characters.
3. Save. Publish to staging.
4. Verify:
   ```js
   Math.max(...[...document.querySelectorAll('style')].map(s=>s.innerHTML.length))
   ```
   Must return ≥ 18,000. If not, the Embed element also got truncated — STOP and report the actual max value. Do not proceed.

Report: `[Step 2] DONE — Stylesheet injected via Embed. Max block = X,XXX chars. Next: Step 3.`

---

## Step 3 — Bind the 8 classes that failed in round 3

Use MCP Designer API (`set_style` + `apply_class`) to bind these classes to these elements. After each binding, publish and verify. If a class doesn't appear in the published DOM after ONE rebind attempt, move on and list it in the final report for manual handling.

| Element | Class to add |
|---|---|
| Skip link `<a>` (first element in body) | `skip-link` |
| Reels grid wrapper `<div>` | `reels__grid` |
| About `<section>` | `about` |
| About inner wrapper `<div>` | `about__inner` |
| Contact `<section>` | `contact` |
| Contact H2 "Hit me." | `contact__h2` (combo with existing `h2`) |
| `<footer>` element | `footer` |
| Footer mark `<img>` | `footer__mark` |

Also: the `.container` count is 3 of 7 expected. Find the 4 section inner wrappers missing `container` class (likely About inner, Contact inner, Footer inner, and one other) and add `container` to them.

Publish after all class changes.

Verify:
```js
({
  skipLink: document.querySelectorAll('.skip-link').length,
  reelsGrid: document.querySelectorAll('.reels__grid').length,
  about: document.querySelectorAll('.about').length,
  aboutInner: document.querySelectorAll('.about__inner').length,
  contact: document.querySelectorAll('.contact').length,
  contactH2: document.querySelectorAll('.contact__h2').length,
  footer: document.querySelectorAll('.footer').length,
  footerMark: document.querySelectorAll('.footer__mark').length,
  container: document.querySelectorAll('.container').length
})
```

Every count must be ≥ 1. `container` must be 7. Any that fail after one rebind attempt → list in report for manual handling.

Report: `[Step 3] DONE — Classes bound. Any unresolved: [list or "none"]. Next: Step 4.`

---

## Step 4 — Fix the 6 broken reel images

Six FPO images have been generated and live at `/Reps and Reels/web/fpo/reel_01.jpg` through `reel_06.jpg` (600×1067, brand-tinted 9:16 FPO cards).

1. Upload all 6 images to Webflow Assets via the MCP Assets API.
2. In each of the 6 `.reel` cards in Designer, replace the broken `<img>` source with the corresponding uploaded asset URL (card 1 → reel_01.jpg, etc.).
3. Ensure each `<img>` has `alt="Reel placeholder"` and is sized to fill its parent (`width: 100%; height: 100%; object-fit: cover;` — either inline or via a Webflow class).

Publish.

Verify:
```js
({
  reelImgs: document.querySelectorAll('.reel img').length,
  reelImgsBroken: [...document.querySelectorAll('.reel img')].filter(i => !i.complete || i.naturalWidth === 0).length
})
```

Must return `{reelImgs: 6, reelImgsBroken: 0}`.

Report: `[Step 4] DONE — 6 FPO images uploaded + bound. Next: Step 5.`

---

## Step 5 — Final verification

Run this single console snippet on staging, paste output verbatim into build log:

```js
({
  styleBlocks: [...document.querySelectorAll('style')].map(s => s.innerHTML.length),
  maxBlock: Math.max(...[...document.querySelectorAll('style')].map(s => s.innerHTML.length)),
  skipLink: document.querySelectorAll('.skip-link').length,
  nav: document.querySelectorAll('.nav').length,
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

Target pass condition:
- `maxBlock` ≥ 18000
- All class counts ≥ 1 (or 6 for `reel` / `reelPlatform`)
- `container` = 7
- `reelImgsBroken` = 0

Report: `[Step 5] DONE — Final verification result: {...}`

---

## Step 6 — Write build log + STOP

Write to `/Reps and Reels/web/build_log_2026-04-19_round4.md`:
- Confirmation that Head Code is cleared
- Embed element location + actual char count injected
- Every class binding that succeeded, every binding that failed + the element ID for Steve to handle manually
- Confirmation that 6 FPO images uploaded (list the Webflow asset URLs)
- Final verification snippet output
- Any remaining work for Steve

Do NOT publish to production. Do NOT touch DNS. Do NOT attempt CMS. Staging only.

---

## Hard stops
- ❌ No production publish
- ❌ No DNS changes
- ❌ No CMS (plan not upgraded, FPO images only)
- ❌ No retrying a failed class bind more than once — flag for manual handling instead
- ❌ No stylesheet minification / edit / cherry-pick — inject literal bytes of `webflow_head_paste.html`
- ❌ No touching copy / text content

## Success criteria
Round 4 is complete when Step 5's verification snippet returns all target values. If any fail after one retry pass, hand them to Steve with element IDs — do not spend more cycles.

Start with Step 1. Report at each step boundary: `[Step X] DONE — <summary>. Next: Step Y.`
