# Round 3 Build Log — 2026-04-19

**Staging URL:** https://reps-reels-320557.webflow.io/
**Verified at:** `?t=5&cb=1` (post Publish #3, cache-busted)
**Verdict:** ⚠️ **Partial. Do NOT publish to production.** Several brief requirements are unmet; surfacing honestly rather than overstating.

---

## Step 1 — Paste stylesheet into Site Settings → Custom Code → Head

- Webflow MCP has no Head Code endpoint (Scripts API caps at 2000 chars, JS-only). Reported blocker; user pasted manually and confirmed push to stage.
- **Verification:** `document.head.querySelectorAll('style').map(s=>s.innerHTML.length)` → `[58, 14252, 949, 1800, 654]`.
- **FAIL vs. brief:** brief required ≥18,000 chars in a single block. Largest block is **14,252** — roughly 4–8KB short of `webflow_head_paste.html` (~22KB). The paste is truncated.
- **Action for user:** re-paste `webflow_head_paste.html` verbatim. Until `max(styleBlocks) ≥ 18000`, later sections of the stylesheet (grid/about/contact/footer rules) are missing, which matches the unstyled DOM observed below.

## Step 2 — Bind BEM classes in Designer

Used Webflow MCP `style_tool` + `element_tool` across two passes + three publishes. Final published-DOM counts:

| Selector | Expected | Actual | Status |
|---|---|---|---|
| `.skip-link` | 1 | 0 | ❌ |
| `.nav` | 1 | 1 | ✅ |
| `.nav__inner` | 1 | 1 | ✅ |
| `.hero` | 1 | 1 | ✅ |
| `.hero__chip` | 1 | 1 | ✅ |
| `.hero__h1` | 1 | 1 | ✅ |
| `.voice` | 1 | 1 | ✅ |
| `.voice__cornerstone` | 1 | 1 | ✅ |
| `.reels` | 1 | 1 | ✅ |
| `.reels__grid` | 1 | 0 | ❌ |
| `.reel` | 6 | 6 | ✅ |
| `.reel__platform` | 6 | 6 | ✅ |
| `.about` | 1 | 0 | ❌ |
| `.about__inner` | 1 | 0 | ❌ |
| `.contact` | 1 | 0 | ❌ |
| `.contact__h2` | 1 | 0 | ❌ |
| `.footer` | 1 | 0 | ❌ |
| `.footer__mark` | 1 | 0 | ❌ |
| `.container` | 7 | 3 | ❌ (partial) |

**Case-sensitivity note:** existing Webflow styles "Nav", "Hero", "Container", "Footer", "h1", "h2" (title-case) collided with lowercase BEM creation attempts. Webflow publishes class names lowercase, so `.nav`, `.hero`, `.container`, `.footer`, `.h1`, `.h2` render — those styles pass above. The missing rows were created as new styles via MCP `set_style` (return value: success, both passes), but do not appear in the published DOM after three publishes.

**Suspected cause:** Designer state for skip-link, reels__grid, about, about__inner, contact, contact__h2, footer, footer__mark is not syncing to the publish draft, or Webflow is stripping styles that have no matching stylesheet rule (see Step 1 truncation — if `.about { ... }` isn't in the pasted CSS, Webflow may be treating the class as unused and scrubbing). Either way, needs manual Designer verification in round 4.

## Step 3 — Rebuild 6 reel cards

- ✅ Deleted prior placeholder cards.
- ✅ Inserted 6 new `<a class="reel">` cards via `whtml_builder`; each has `.reel__platform` (6 present).
- ❌ **All 6 reel images broken.** `reelImgsBroken: 6`. Webflow's image proxy rewrote `https://placehold.co/600x1067/0B0B0C/C9A24B?text=Reel+01` → `https://cdn.prod.website-files.com/600x1067/0B0B0C/C9A24B` (404).
- **Fix for round 4:** upload 6 real FPO images to Webflow Assets and reference via the Webflow asset URL, or swap to an FPO host the proxy doesn't rewrite.

## Step 4 — Rotating subtitle inline link

- ✅ Confirmed correct. Actual rendered markup (in `.reels__header`, not `.hero`):
  > `<p class="reels__fallback-note">Recent. The rest is on IG and TikTok — <a ... href="https://instagram.com/repsreels" ...>@repsreels</a> on both.</p>`
- `@repsreels` is a single inline anchor; sentence reads as one line.

## Step 5 — Browser console verification

Ran on `https://reps-reels-320557.webflow.io/?t=5&cb=1` post Publish #3:

```json
{
  "styleBlocks": [58, 14252, 949, 1800, 654],
  "skipLink": 0,
  "nav": 1, "navInner": 1,
  "hero": 1, "heroChip": 1, "heroH1": 1,
  "voice": 1, "voiceCornerstone": 1,
  "reels": 1, "reelsGrid": 0,
  "reel": 6, "reelPlatform": 6,
  "reelImgs": 6, "reelImgsBroken": 6,
  "firstImg": "https://cdn.prod.website-files.com/600x1067/0B0B0C/C9A24B",
  "about": 0, "aboutInner": 0,
  "contact": 0, "contactH2": 0,
  "footer": 0, "footerMark": 0,
  "container": 3
}
```

**Pass: 12 / Fail: 10** (class bindings 6, container shortfall, stylesheet truncation, 6 broken reel images collapsed into one image-health failure).

## Step 6 — STOP

Per brief: not publishing to production. Staging only.

---

## What round 4 needs

1. **Re-paste the stylesheet.** Target `max(styleBlocks) ≥ 18000`. Current = 14,252.
2. **Manually bind in Designer** the 8 classes that did not persist via MCP: `skip-link`, `reels__grid`, `about`, `about__inner`, `contact`, `contact__h2`, `footer`, `footer__mark`. MCP `set_style` succeeded but publish output doesn't reflect it.
3. **Bring `.container` count from 3 → 7.** Four wrappers are still unclassed.
4. **Fix 6 broken reel images.** Upload FPO images to Webflow Assets; the image proxy breaks `placehold.co` URLs.

The pattern across rounds 1–3: verbatim CSS paste does not land in full, and MCP-driven class binding does not always reach the publish pipeline. Round 4 should treat both as manual Designer tasks.
