# Staging QA + Fix List — Round 1
**URL reviewed:** https://reps-reels-320557.webflow.io/
**Date:** 2026-04-19
**Reviewer:** Structural + visual review against `homepage_prototype.html` and `design_direction.md`.
**Status:** ❌ **Not launch-ready.** Major CSS gap below the voice moment section. Fixing is cheap but required.

---

## TL;DR

Structure, IA, copy, and nav all shipped correctly. **Styling didn't.** Below the voice moment, the page renders as raw unstyled HTML — no container widths, no typography scale, default bullet markers, default heading sizes, footer collapsed to the left edge. The reels grid has lost its grid layout entirely.

Best guess: the prototype's `<style>` block was not ported to Webflow as custom head code (or only partially ported), and most elements have no Webflow class overrides in its place. The sections Webflow *did* style natively (nav, hero, voice moment) look correct.

Fix path: paste the full prototype stylesheet into **Site Settings → Custom Code → Head** (or into Designer as global styles on matching class names). Either route gets you there — the simpler one for Claude Code is the custom-code paste.

---

## Issues — ordered by severity

### 🔴 CRITICAL — Launch-blocking

#### 1. Global stylesheet is missing from below-the-fold sections
**What:** About / Hit me / Contact / Footer render fully unstyled. Default browser fonts, default font sizes, default list markers, no container padding, no layout.
**Evidence:** "About" heading renders as default `<h2>` (roughly 24px, left-flushed to x=0). Contact list shows default `disc` bullet markers. Footer has no layout.
**Expected:** `design_direction.md` §3–§5 typography + spacing scale, `homepage_prototype.html` `.section`, `.container`, `.h-display-xl`, `.body-lg`, etc.
**Fix:** Paste the entire `<style>...</style>` block from `homepage_prototype.html` lines 57–~720 into **Site Settings → Custom Code → Head**, wrapped in `<style>` tags. Then in Designer, add the class names (`section`, `container`, `h-display-xl`, `h-display-md`, `eyebrow`, `body-lg`, etc.) to the matching Webflow elements so the CSS actually targets them.
**Alternative, cleaner fix:** Convert the prototype CSS into Webflow Designer classes + variables via the MCP. This is the "right" way but costs more agent time. Start with the custom-code paste — if it fixes the problem in one shot, ship it.

#### 2. Reels grid has lost its grid layout and card styling
**What:** The 6 placeholder reel cards render as a single vertical stack of tiny ~40px blue play-button SVGs with label text above them. No 3-column grid. No 9:16 card aspect. No card background, padding, border, or brass hover state. Platform chip ("IG" / "TIKTOK") is unstyled mono text, not the styled chip from the prototype.
**Expected:** `reels_embed_spec.md` §5 Collection List markup + `homepage_prototype.html` `.reels-grid`, `.reel-card`, `.reel__media`, `.reel__meta`, `.reel__platform`, `.reel__caption`.
**Fix:** Same root cause as #1 — the grid CSS is not applied. After the global stylesheet paste, add class names to the reel card container (`.reels-grid` → `display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px;`) and each card (`.reel-card`, aspect-ratio 9/16, brass border on hover, etc.).
**Also:** the "Placeholder" link wrapper element and the generic play-icon SVG need to be replaced with the embed template from `reels_embed_spec.md` §5 (with `data-embed-url` attribute + wrapped `<a>` with caption/meta structure). They're not wired to lazy-load.

#### 3. Hero has ~500px of empty black space above the eyebrow
**What:** The hero section fires up at roughly the middle of the viewport. There's a massive dark band between the top of the section and the "RR · THE OVERLAP" eyebrow.
**Expected:** Hero should have measured top padding (prototype uses `padding: clamp(80px, 12vw, 160px) 0` per `design_direction.md` §5 spacing scale). Something is adding either a min-height ≥ 100vh or excessive top padding.
**Fix:** Inspect the hero section's top padding / min-height in Designer. Remove any extra spacer divs. Verify against the prototype — the eyebrow should appear roughly 120-160px from the top of the section, not 500+px.

---

### 🟠 HIGH — fix before publishing to production

#### 4. Big visual gap between hero and voice moment
**What:** Roughly 300-400px of empty dark space between the hero's subhead and the "Not a fitness page..." paragraph.
**Root cause:** Same as #3 — over-padded sections. The hero has bottom padding, then the next section has top padding, and both are inflated.
**Fix:** Normalize section padding to `padding: clamp(64px, 8vw, 120px) 0` top/bottom per `design_direction.md`. Also verify there isn't a hidden empty container between sections.

#### 5. Voice moment pullquote is rendering correctly, but has no gold left rule at small widths — verify on mobile
**What:** At desktop the 4px brass left border on "THE YEARS ARE INVISIBLE. / THE RESULT ISN'T." is rendering. Couldn't verify mobile because the webflow.io viewport-meta forces 1533px even on resized window.
**Fix:** Open staging on an actual phone (or mobile emulation in DevTools). Confirm the gold left rule persists at 375px. Should be 3px min.

#### 6. Rotating section's trailing "Recent. The rest is on IG and TikTok — @repsreels on both." has lost the `@repsreels` link
**Evidence:** DOM shows `generic "Recent. The rest is on IG and TikTok — on both."` — the `@repsreels` text has been pulled out as a separate link above the subtitle. Copy reads awkwardly.
**Expected:** Inline: "Recent. The rest is on IG and TikTok — @repsreels on both." with `@repsreels` as the single link.
**Fix:** Rebuild that paragraph in Designer as one text block, add the link via Webflow's text-link picker.

#### 7. Reel card captions are missing and Reel meta structure is broken
**What:** Cards are rendering as `PLATFORM / caption / SVG` stacked (label above image), not the prototype's `media / meta (platform + caption)` layout.
**Fix:** Rebuild the reel card to match `reels_embed_spec.md` §5 markup:
```
<a class="reel-card" data-embed-url="...">
  <div class="reel__media">[embed-or-placeholder]</div>
  <div class="reel__meta">
    <span class="reel__platform">IG</span>
    <p class="reel__caption">...</p>
  </div>
</a>
```

---

### 🟡 MEDIUM — fix before Phase 2 QA

#### 8. Footer logo is the B&W placeholder
**What:** Silhouette bagpipe mark (B&W) is in footer. You flagged this — item 3 of your post-build list.
**Fix:** After gold assets are uploaded, swap to `brand_mark_primary_gold.png` at 140-160px wide, single standalone mark (no text wordmark next to it — the manifesto mark already includes "REPS & REELS / QUIET WORK WINS.").

#### 9. Nav logo mark is rendering as generic round icon — verify it's `rr_sticker.png`
**Fix:** After gold upload, confirm nav logo = `rr_sticker.png` at 28px. Currently shows a small gold circle which might be a Webflow placeholder.

#### 10. "Occasional email. Not a newsletter exactly." + "Omnisend form embeds here." are rendering as two stacked plain text generics
**What:** The Omnisend slot is just a `<div class="rr-omni-slot">` placeholder label, which is expected — but the wrapper isn't styled.
**Fix:** Wrap in the prototype's `.email-signup` card styling (dark inset, brass rule on top, subtle border). After styles are applied, paste the real Omnisend embed into the slot (item 6 of your post-build list).

#### 11. Skip-link is visible at page load
**What:** "Skip to content" is rendering at x=0, y=0 (visible, not positioned off-screen).
**Expected:** Per `.skip-link` CSS — positioned at `top: -40px`, only visible on focus.
**Fix:** Also resolved by fixing #1 (global stylesheet paste).

---

### 🟢 LOW — can defer

#### 12. Analytics widget (Plausible, per launch_checklist.md §1.9) is not present
**Status:** Expected at this phase. Not blocking staging QA.
**Fix:** Phase 6.9 of claude_code_build_brief.md — paste Plausible script into Site Settings → Custom Code → Head.

#### 13. OG image still a placeholder / doesn't exist
**Status:** Already on your post-build list (item 1). Blocking production publish, not staging.

---

## Technical verification results

| Check | Result |
|---|---|
| Page renders without hard errors | ✅ |
| All spec sections present in DOM (hero / voice moment / reels / about / hit me / footer) | ✅ |
| Copy matches `homepage_copy.md` | ✅ (one inline link broken — see #6) |
| Nav links correct (IG + TikTok) | ✅ |
| Hero H1 uses display type at desktop | ✅ (hero is the only section that shipped with its styles) |
| Voice moment renders with brass left rule | ✅ |
| Reels grid uses 3-column layout | ❌ |
| Reel cards use 9:16 aspect + card styling | ❌ |
| About / Hit me / Contact / Footer styled | ❌ |
| Mobile breakpoint verified | ⚠️ Not verified — webflow.io doesn't respect resize. Open on real device. |
| JSON-LD Person schema in head | ⚠️ Not verified — JS exec blocked by webflow.io CSP. Check in Site Settings or View Source. |
| OG tags in head | ⚠️ Same as above — verify via View Source or Site Settings. |
| Canonical URL in head | ⚠️ Same. This is your post-build item 4. Must be `https://www.repsreels.com/`. |

---

## Recommended fix sequence for Claude Code (round 2)

**Estimated: 30-60 minutes of agent time.**

1. **Open Site Settings → Custom Code → Head.** Paste the `<style>...</style>` block from `homepage_prototype.html` lines 57–(closing `</style>`). This is the single highest-leverage fix.
2. **In Designer, add Webflow class names to the elements that should receive those styles.** The stylesheet uses semantic class names (`.section`, `.container`, `.hero`, `.h-display-xl`, `.eyebrow`, `.reels-grid`, `.reel-card`, etc.). Match Webflow element classes to those selectors. This is the tedious part.
3. **Fix the reel card markup** per `reels_embed_spec.md` §5 — swap the "Placeholder" link + generic SVG for the `.reel-card` → `.reel__media` + `.reel__meta` structure.
4. **Tighten hero top padding** — remove excess spacer divs / over-padded wrapper. Target: eyebrow visible within 120-160px of section top.
5. **Fix the reels subtitle link** — inline `@repsreels` in the single sentence, not pulled out separately.
6. **Re-verify via staging URL.** Screenshot hero, voice moment, reels grid, about, footer. Compare to `homepage_prototype.html` open in a browser side-by-side.
7. **Then proceed with your post-build to-do list items 1-6** (gold assets, canonical check, favicon, Omnisend embed, archive unused pages).

---

## Callouts worth debating

- **Should we keep pushing CSS through Site Settings → Custom Code, or convert to proper Webflow Designer classes/variables?** Custom code is faster today but harder for future non-Claude edits in Designer. If this site ships now and gets maintained via Claude Code forever, custom code is fine. If Steve (or anyone) will edit in Designer directly, convert to native Webflow classes. **Recommendation for R&R: custom code paste first to unblock launch, then migrate to native classes in a round 3 polish pass.**
- **Is the reels grid worth fixing before CMS hosting is enabled?** Yes — the grid layout, card styling, and markup need to be right so when CMS items replace the 6 placeholders, they just drop into the existing shell. Don't defer this.

---

## What's NOT in this fix list

- CMS Collection build (Phase 2, requires plan upgrade — already on your post-build list)
- OG image production (your post-build item 1)
- Real gold asset swap (your post-build items 2-3)
- Omnisend embed paste (your post-build item 6)
- Favicon assignment (your post-build item 5)
- Canonical URL correction (your post-build item 4)
- Page archiving (your post-build item 7)

Those are all correctly queued on your side. This doc is only about the visual regressions that showed up in the build itself.
