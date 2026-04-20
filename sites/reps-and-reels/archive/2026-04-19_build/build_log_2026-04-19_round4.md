# Round 4 Build Log — 2026-04-19

**Staging URL:** https://reps-reels-320557.webflow.io/
**Verified at:** `?cb=r4final` (post Publish #3)
**Verdict:** ⚠️ **Partial pass.** Stylesheet and FPO images fully resolved via a strategy shift. 10 BEM class bindings and 3 `.container` additions still require manual Designer action — same root cause as round 3 (MCP `set_style` returns success but Designer state does not persist on elements that start with empty `styleNames`).

Do NOT publish to production.

---

## Route selected

User chose **Route A** after round 4 prompt analysis surfaced three plan mismatches:
- `webflow_head_paste.html` is 14,473 bytes (not ~22KB) — the plan's "≥18,000" target was unattainable with the current file.
- Webflow MCP has no "Embed" element type. Substituted a DOM `<style>` element (`dom_tag: "style"` + `set_text`).
- `whtml_builder`'s `css` parameter rejects `@keyframes` (CSS has 1) and non-Webflow `min-width` media queries (CSS has 16). Not a viable route.

Route A: DOM `<style>` element prepended to body, target revised to `maxBlock ≥ 14,000`.

---

## Step 1 — Clear Head Code

**Not executed via MCP.** `data_scripts_tool` supports inline site scripts (2KB cap, JS-only) and has no Head-Code-clear endpoint. Left Head Code untouched. Harmless — the new DOM `<style>` lives later in the document, so its rules win cascade ties.

**Manual action for Steve:** Open Site Settings → Custom Code → Head Code and clear the existing truncated paste. Confirm before next production push.

## Step 2 — Stylesheet injection ✅

- Created DOM element, `dom_tag: "style"`, prepended to `<body>`.
  - Element ID: `{component: 69322470da6d6f12bcacc3bb, element: 21e355f7-3ce4-0616-0794-a38a3ebcb071}`
- Set full CSS via `set_text` (14,252 chars; stripped outer `<!-- -->` comment and `<style>` wrapper before injection).
- Published. Verified on staging HTML:
  - `styleBlocks`: `[6806, 6724, 14326]`
  - `maxBlock`: **14,326** (14,252 CSS + ~74 chars Webflow wrapper).
  - Presence confirmed for rules: `.skip-link`, `.reels__grid`, `.about {`, `.contact {`, `.footer {`, `--forge-black`.

`maxBlock` target was 18,000 in the round 4 prompt but was unattainable because the source file is only 14,473 bytes. Revised target of ≥14,000 met cleanly.

## Step 3 — Class bindings ⚠️

Attempted 11 bindings in one batched `set_style` call. MCP returned `status: success` for all 11. Re-query of the same elements 5 seconds later showed 10 of them with `styleNames: []` again — Designer state reverted. Published DOM confirms the revert.

**Landed (1):** Nav element (`d29cfaca-ee47-cfa0-6881-a231d066e060`) gained `Container` as a combo class on top of the existing `Nav` + `nav__inner`. `.container` count went from 3 → 4.

**Reverted (10) — require manual Designer handling:**

| Intended class | Target element ID |
|---|---|
| `skip-link` | `beea3561-6deb-e2af-8494-84bfe4415671` (skip Link) |
| `reels__grid` | `758b60af-26b5-72f0-ea91-823d039d999c` (reels grid wrapper Block) |
| `about` | `5b841d62-64ec-6a73-108d-f7d254148fcc` (about Section) |
| `about__inner` + `Container` | `5b841d62-64ec-6a73-108d-f7d254148fcb` (about inner Block) |
| `contact` | `339ea392-a011-5fb2-d1f1-cd138afc534d` (contact Section) |
| `h2` + `contact__h2` | `339ea392-a011-5fb2-d1f1-cd138afc533a` (contact H2 "Hit me.") |
| `Container` | `339ea392-a011-5fb2-d1f1-cd138afc534c` (contact inner Block) |
| `Footer` | `5634b569-6fcd-0048-91dd-9b69cc96f54e` (footer Block) |
| `Container` | `5634b569-6fcd-0048-91dd-9b69cc96f54d` (footer inner Block) |
| `footer__mark` | `5634b569-6fcd-0048-91dd-9b69cc96f53c` (footer Image) |

**Root-cause hypothesis:** The Webflow MCP Designer API cannot persist style changes on elements that were imported with empty `styleNames`. The nav element (which started with styles) accepted its Container combo without issue. The 10 zero-style elements all showed the same "success then revert" pattern — same as round 3. Retrying is pointless per plan; flagging for manual handling.

**Steve's manual Designer steps:** select each element ID above in the Designer (via the IDs panel or by clicking the element) and apply the class name(s) from the middle column. All class definitions already exist in the site's Styles panel — no new style creation needed.

## Step 4 — 6 FPO images ✅

All 6 reel placeholder FPO images were already present in Webflow Assets from a prior partial run. Bound each to the corresponding reel card's inline `<img>` via `set_image_asset`:

| Reel card | Asset ID | URL |
|---|---|---|
| reel 1 (IG) | `69e55965b626c4a0230d079b` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e55965b626c4a0230d079b_reel_01.jpg |
| reel 2 (TikTok) | `69e55965501919122d6ac3f1` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e55965501919122d6ac3f1_reel_02.jpg |
| reel 3 (IG) | `69e5596503f7942b97e0233a` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e5596503f7942b97e0233a_reel_03.jpg |
| reel 4 (TikTok) | `69e55965d087f9d9d2fd1b48` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e55965d087f9d9d2fd1b48_reel_04.jpg |
| reel 5 (IG) | `69e55965deaa6281cac3aaaf` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e55965deaa6281cac3aaaf_reel_05.jpg |
| reel 6 (TikTok) | `69e55965f63ac5ac24e2650f` | https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/69e55965f63ac5ac24e2650f_reel_06.jpg |

Publish output: `reelImgsBroken: 0`. All 6 FPO images render via Webflow's CDN.

Note: reel 1 has a duplicate `<img>` inside its `.reel__placeholder` div — an artifact from a prior-round attempt that inserted a new Image element without removing the original. Visually both render; not harmful, but can be cleaned up manually when Steve re-enters the Designer.

## Step 5 — Final verification

Run on `https://reps-reels-320557.webflow.io/?cb=r4final` post Publish #3 (via curl + regex, not a live browser console):

```json
{
  "styleBlocks": [6806, 6724, 14326],
  "maxBlock": 14326,
  "skipLink": 0,
  "nav": 1,
  "hero": 1,
  "heroH1": 1,
  "voice": 1,
  "voiceCornerstone": 1,
  "reels": 1,
  "reelsGrid": 0,
  "reel": 6,
  "reelPlatform": 6,
  "reelImgsBroken": 0,
  "about": 0,
  "aboutInner": 0,
  "contact": 0,
  "contactH2": 0,
  "footer": 0,
  "footerMark": 0,
  "container": 4
}
```

Pass/fail against round 4 acceptance criteria:

| Criterion | Target | Actual | Status |
|---|---|---|---|
| `maxBlock` | ≥18,000 (revised ≥14,000) | 14,326 | ✅ (revised) |
| `reelImgsBroken` | 0 | 0 | ✅ |
| `container` | 7 | 4 | ❌ (needs 3 more) |
| 8 BEM class counts | each ≥1 | 0 for all 8 | ❌ (manual) |
| styles retained from round 3 | all | 9/9 | ✅ |

## Step 6 — Remaining work for Steve

Manual Designer actions required before any production publish:

1. **Clear Site Settings → Custom Code → Head Code** (contains truncated 14,252-char paste from round 3). Not strictly required because the DOM `<style>` element now carries the full stylesheet, but recommended for cleanliness.
2. **Apply classes in the Designer** to each of the 10 elements listed in Step 3. All class names already exist in Styles; just select and apply.
3. **Optionally remove duplicate reel 1 `<img>`** (element `e2b1c9bc-aeab-828b-1f51-278e7371c220`) to leave only the `inline-img-0` img with asset bound.

Hard stops respected: no production publish, no DNS changes, no CMS, no copy edits.

---

## Round 4 strategy change that worked

**DOM `<style>` element with `set_text` of raw CSS was the right bypass.** Head Code paste field truncates at ~13–14KB; the DOM-style path has no observable limit (carried 14,252 chars cleanly through publish). For any future round needing another CSS change, update `set_text` on element `21e355f7-3ce4-0616-0794-a38a3ebcb071` — no Head Code paste needed.

## Root cause that remains unsolved

Webflow MCP `set_style` against elements with empty `styleNames` returns success but does not persist. Confirmed across round 3 (original reporter) and round 4 (independent reproduction on the same 10 elements). One successful Designer-state binding this round — the nav's Container combo — landed because the target already had styles. This suggests the MCP's style application path requires a pre-existing style anchor on the element. Until that's resolved, any element created or imported without an initial class list will need a manual Designer step to receive its first class.
