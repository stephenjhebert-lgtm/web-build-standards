# Claude Code — Round 3 Fix Prompt (Reps & Reels)
**Paste everything below this line into a fresh Claude Code session.**

---

You previously attempted rounds 1 and 2 of the Reps & Reels Webflow build at `https://reps-reels-320557.webflow.io/`. Both rounds failed the same way: the global stylesheet was never fully pasted into Webflow, and the Designer elements were never bound to the CSS class names. Round 3 fixes both, using a paste-ready stylesheet file and the CORRECT BEM class names from the actual prototype.

**Read these first, in this order:**
1. `/Reps and Reels/web/staging_qa_round2.md` — what failed in round 2 and why
2. `/Reps and Reels/web/webflow_head_paste.html` — the literal stylesheet to paste (do not modify)
3. `/Reps and Reels/web/homepage_prototype.html` — source of truth for markup + classes
4. `/Reps and Reels/web/reels_embed_spec.md` §5 — reel card markup structure

## Prerequisites
- Webflow MCP connected (`/mcp`)
- Bridge App running in `https://reps-reels-320557.design.webflow.com`
- Site ID captured

If any fail, stop and report.

## Important context from Steve
- **No CMS.** Plan upgrade is deferred. Use FPO (placeholder) images directly inside each reel card's `.reel__placeholder` div — no Collection List. 6 cards total. Steve will swap to real thumbs + CMS later.
- **Do not minify the stylesheet.** Do not cherry-pick. Paste the literal bytes of `webflow_head_paste.html` end-to-end.
- **Do not improvise class names.** Use the mapping table below exactly.

---

## Step 1 — Paste the stylesheet literally into Site Settings → Custom Code → Head

1. Open Webflow Site Settings → Custom Code → Head Code via MCP.
2. Delete anything currently in that field.
3. Read the literal contents of `/Reps and Reels/web/webflow_head_paste.html` (the entire file, including `<style>` and `</style>` tags).
4. Paste those literal bytes into the Head Code field.
5. Save.
6. Publish to staging (do NOT publish to production).

**Verify before continuing.** Load `https://reps-reels-320557.webflow.io/` in a browser and run in the console:

```
document.head.querySelectorAll('style').length
```

Then for each style block index N (0, 1, 2, 3, 4...), check:
```
document.head.querySelectorAll('style')[N].innerHTML.length
```

At least one block must be ≥ 18,000 characters. If every block is under 10,000 chars, the paste failed — re-paste. Do not move on until one block is ≥ 18,000 chars.

**If the MCP custom-code endpoint won't accept a 22KB paste, STOP and report to Steve. Do not fall back to compression or cherry-picking. That is the failure mode that sank rounds 1 and 2.**

Report: `[Step 1] DONE — Pasted XX,XXX chars into Head Code. Block [N] verified at YY,YYY chars. Next: Step 2.`

---

## Step 2 — Bind Webflow classes to elements using the CORRECT prototype BEM names

The prototype uses BEM-style class names. Round 2 used the wrong names (`.section`, `.container`, `.h-display-xl`, `.reels-grid`, `.reel-card`, `.reel__media`, `.reel__meta`, etc.) — **ignore all prior class-name guidance. Use only the table below.**

For each element in Designer, add the listed class name(s). If an element already has a different class, rename it to match. Combo classes are additive — an element can carry multiple classes (e.g. the hero chip carries both `hero__chip` and `mono`).

### Global
| Element | Class |
|---|---|
| Skip-to-content link (first in `<body>`) | `skip-link` |
| Every section's inner max-width wrapper | `container` |

### Nav
| Element | Class |
|---|---|
| `<header>` / nav wrapper | `nav` |
| Inner wrapper (flex row) | `nav__inner` |
| Wordmark link "REPS & REELS" | `nav__wordmark` |
| Logo `<img>` inside wordmark | `nav__mark` |
| Right side group (IG + TikTok icon links) | `nav__right` |
| Each social icon link (IG, TikTok) | `nav__social` |

### Hero section
| Element | Class(es) |
|---|---|
| `<section>` hero wrapper | `hero` |
| Background image wrapper `<div>` | `hero__bg` |
| Content wrapper `<div>` (inside container) | `hero__content` |
| Eyebrow chip "RR · THE OVERLAP" | `hero__chip` + `mono` |
| H1 "Two hobbies..." | `hero__h1` + `h1` |
| Subhead paragraph | `hero__subhead` + `subhead` |

### Voice moment section
| Element | Class |
|---|---|
| `<section>` wrapper | `voice` |
| Inner container | `voice__inner` |
| First body paragraph | `voice__body` |
| Pullquote paragraph (gets brass left rule) | `voice__cornerstone` |

### Rotating / Reels section
| Element | Class(es) |
|---|---|
| `<section>` wrapper | `reels` |
| Header wrapper (label + h2 + copy) | `reels__header` |
| Eyebrow label "ROTATING" | `reels__label` + `mono` |
| H2 "What's currently rotating." | `h2` |
| Grid wrapper `<div>` | `reels__grid` |
| Each of 6 reel card `<a>` | `reel` |
| Platform chip span inside card ("IG" / "TIKTOK") | `reel__platform` |
| Placeholder div inside card (will hold FPO image) | `reel__placeholder` |
| Caption paragraph inside card | `reel__caption` |
| Trailing "Recent. The rest is on IG and TikTok — @repsreels on both." | `reels__fallback-note` |

### About section
| Element | Class(es) |
|---|---|
| `<section>` wrapper | `about` |
| Inner wrapper | `about__inner` |
| Eyebrow label "ABOUT" | `about__label` + `mono` |
| H2 "About" | `about__heading` + `h2` |
| Body wrapper | `about__body` |

### Contact / "Hit me" section
| Element | Class(es) |
|---|---|
| `<section>` wrapper | `contact` |
| Eyebrow label "CONTACT" | `contact__label` + `mono` |
| H2 "Hit me." | `contact__h2` + `h2` |
| Body paragraph | `contact__body` |
| Email mailto link | `contact__email-link` |
| Email signup block wrapper | `contact__form-block` |
| Signup copy paragraph | `contact__form-copy` |
| Omnisend slot `<div>` | `contact__form-placeholder` |

### Footer
| Element | Class |
|---|---|
| `<footer>` | `footer` |
| Top block (manifesto mark + tagline) | `footer__top` |
| Wordmark / mark wrapper | `footer__wordmark` |
| Manifesto mark `<img>` | `footer__mark` |
| Tagline paragraph | `footer__tagline` |
| Bottom row (social + email + copy) | `footer__row` |
| Social links wrapper | `footer__social` |
| Email span | `footer__email` |
| Copyright paragraph | `footer__copy` |

Report: `[Step 2] DONE — Bound all classes. Next: Step 3.`

---

## Step 3 — Rebuild reel cards with FPO images (no CMS)

Steve has NOT enabled CMS. Do not attempt a Collection List. Build 6 static `<a class="reel">` cards directly in Designer, each with this structure:

```html
<a class="reel" href="[post-url]" target="_blank" rel="noopener">
  <span class="reel__platform">IG</span>
  <div class="reel__placeholder">
    <!-- FPO image fills this div -->
  </div>
  <p class="reel__caption">[caption snippet]</p>
</a>
```

For each card's `.reel__placeholder`:
- Insert a single `<img>` using a placeholder source — acceptable options: `https://placehold.co/600x1067/0B0B0C/C9A24B?text=Reel+01` through `Reel+06` (matches the brand colors — black bg, brass text), or any similar FPO service.
- Set `<img>` to fill its parent (`width: 100%; height: 100%; object-fit: cover;` inline or via an added Webflow class — the `.reel__placeholder` parent already has `aspect-ratio: 9/16` from the pasted stylesheet).
- Alt text: `"Reel placeholder"` (generic — Steve will replace with real thumbs later).

Platform chip text alternates: IG, TIKTOK, IG, IG, TIKTOK, IG (or whatever existing pattern is in the prototype — match the prototype order).

Captions: reuse the placeholder caption text that's already on the cards — don't rewrite copy.

Report: `[Step 3] DONE — 6 cards rebuilt with FPO images. Next: Step 4.`

---

## Step 4 — Fix Rotating subtitle inline link

Current state: "Recent. The rest is on IG and TikTok — on both." with `@repsreels` extracted as a separate link above.

Target: One sentence — `Recent. The rest is on IG and TikTok — @repsreels on both.` — where `@repsreels` is an inline link to `https://www.instagram.com/repsreels/`.

Report: `[Step 4] DONE — Inline link restored. Next: Step 5.`

---

## Step 5 — Verify via browser console

Load `https://reps-reels-320557.webflow.io/` and run each of these one at a time:

```
document.querySelector('.skip-link')                     // not null
document.querySelector('.nav')                           // not null
document.querySelector('.hero')                          // not null
document.querySelectorAll('.hero__h1').length            // 1
document.querySelector('.voice__cornerstone')            // not null
document.querySelector('.reels__grid')                   // not null
document.querySelectorAll('.reel').length                // 6
document.querySelectorAll('.reel__placeholder img').length // 6
document.querySelector('.about__inner')                  // not null
document.querySelector('.contact__h2')                   // not null
document.querySelector('.footer__mark')                  // not null (an <img>)
```

Any `null` or wrong count = that element's class binding is still wrong. Go back to Step 2 for that element.

Also visually confirm:
- Hero eyebrow appears ~120-160px from top of hero section (not 500px+).
- Voice moment pullquote has 4px brass left border.
- Reels grid is 3-column at desktop (≥1024px), 2-column at tablet (768-1023px), 1-column at mobile.
- About / Contact / Footer all render in styled type (Fustat body, Big Shoulders Display headings) — not browser defaults.
- Footer manifesto mark is centered and visible.

Report: `[Step 5] DONE — All assertions pass. Visual match to prototype confirmed.`

---

## Step 6 — Write the build log

Write to `/Reps and Reels/web/build_log_2026-04-19_round3.md`:
- Chars pasted into Head Code (and which `<style>` block index holds the main stylesheet)
- Every class that was added or renamed in Designer (by element)
- Confirmation that 6 FPO cards were built, with the placeholder image source used
- Subtitle link fix confirmation
- Console verification output (paste the actual returned values)
- Any step that failed + why

Then STOP. Do not publish to production.

---

## Hard stops
- ❌ No production publish
- ❌ No DNS changes
- ❌ No CMS attempt (plan not upgraded)
- ❌ No content deletion without backup
- ❌ No copy rewrites
- ❌ No stylesheet minification / cherry-picking — literal paste only

## Success criteria
Round 3 is complete when every console assertion in Step 5 passes AND staging visually matches `homepage_prototype.html` across hero / voice / reels / about / contact / footer at desktop, tablet (768px), and mobile (375px).

Start with Step 1. Report at each step boundary using the format: `[Step X] DONE — <summary>. Next: Step Y.`
