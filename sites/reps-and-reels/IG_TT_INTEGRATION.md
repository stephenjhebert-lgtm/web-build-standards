# Instagram + TikTok Integration — Punch List

**Owner:** Steve
**Status:** Site is live on staging with 6 placeholder reel cards. This doc is the path to replacing those placeholders with real, lazy-loaded reel embeds.
**Reference spec:** `reels_embed_spec.md` (unchanged — still the technical source). This punch list is just the ordered task breakdown.

---

## Current state

- 6 static reel cards on the homepage: `.reel` → platform chip (IG/TT) → placeholder "[ Reel embed ]" → caption.
- Captions have seed copy ("Kitchen chanter. Errors left in." etc.) that should be replaced with real post captions as reels are wired.
- No CMS Collection exists yet. Cards are hand-placed Blocks in the Webflow Designer.

## Target state

- A Webflow CMS Collection named `Reels` drives the 6 homepage cards.
- Each card shows a static placeholder on page load and loads the real IG / TikTok iframe only when it enters viewport (or on tap).
- Adding a new reel = 30 seconds of CMS entry by Steve. No dev involvement.
- Lighthouse score stays intact — no third-party JS on initial page load.

---

## The punch list

### Phase 1 — CMS + embed wiring (one-time setup, ~90 min)

**1. Create the `Reels` CMS Collection**
In Webflow Designer → CMS Collections → New Collection → "Reels". Add these fields (exact slugs matter — see `reels_embed_spec.md §2`):

- `name` — Plain text, required (admin-only label)
- `platform` — Option, required, options: `Instagram`, `TikTok`
- `post-url` — Link, required
- `caption-snippet` — Plain text, 80 char max
- `date-posted` — Date, required
- `featured` — Switch
- `hide-from-homepage` — Switch

**2. Seed the Collection with 6 real reels**
For each: copy post URL from IG or TikTok, paste into a new CMS item, fill in platform + date + a one-line caption snippet. Mix of IG and TikTok is fine. Use the captions already on the site as a shortlist of the vibe (kitchen chanter, heavy singles, post-lift chanter, chest day, warm-up chanter, year X on tune) but replace with real post specifics.

**3. Replace the 6 hand-placed reel Blocks with a Collection List**
This is the biggest structural move. Either:
- **Option A (clean):** Delete the current 6 `.reel` Blocks inside `.reels__grid`. Drop in a Collection List bound to `Reels`, filter `hide-from-homepage = false`, sort `featured` desc then `date-posted` desc, limit 6. Style the wrapper to match the existing grid. Inside each Collection Item use an **HTML Embed** block with the template in `reels_embed_spec.md §5`.
- **Option B (faster if CMS is a lift):** Keep the 6 static Blocks, hand-edit each to hard-code real post URLs and captions. Lose the dynamic benefit but ship faster. Can upgrade to Option A later.

Recommendation: go Option A. It's 30 min now vs. rework every time a reel changes.

**4. Paste the lazy-load script into Webflow Custom Code → Before `</body>`**
The full script is in `reels_embed_spec.md §6`. It uses `IntersectionObserver` to swap placeholders for real iframes only when the card enters viewport. Includes error fallback and "Open on IG/TikTok" deeplink if the iframe fails.

**5. Publish to staging, test on a real device**
- iOS Safari: both embed types load without horizontal scroll.
- Android Chrome: IG + TT embeds render.
- Desktop: hover states on placeholder look intentional.
- Slow connection throttle: placeholders stay branded until iframe loads, no layout shift.

**6. CLS / layout-shift check**
Each card needs an explicit aspect ratio so the iframe doesn't push content when it loads. Verify 9:16 box stays locked.

---

### Phase 2 — content ops (ongoing, per-post)

**7. Write the "posting → site" checklist**
Add a 4-step routine to `../Content/posting_log.md` or the weekly review:
1. Post to IG (or TikTok).
2. Copy post URL from share menu.
3. Webflow Editor → Reels → New Reel → paste URL + platform + date + caption snippet.
4. Publish. Done.

**8. Decide on the 6-card rotation rule**
Today: newest 6. Do you want:
- a) strict newest 6 (drops the 7th reel from site when a new one posts)
- b) newest 4 + 2 pinned "evergreen" (use `featured` switch)
- c) newest 3 IG + newest 3 TikTok (requires a second Collection List or filter per-card)

Pick (a) to start; revisit at week 4 if something's worth pinning.

**9. Set a caption-snippet convention**
Keep it under 80 chars. Tone match: "Kitchen chanter. Errors left in." Not a tagline, not a description — one honest line. Always include the specific thing (tune, lift, setup).

---

### Phase 3 — polish (post-launch, week 2)

**10. Add `/reels` page (future)**
If engagement warrants: a dedicated `/reels` page that shows the full Collection (not just 6). Re-uses the same CMS. Maybe 2 hours of work when ready.

**11. Track embed load rate**
If the Webflow Analytics or a plugged-in GA setup can capture it: what % of visitors actually load an embed vs. just scroll past? Informs whether to auto-load the first card on viewport entry vs. require tap.

**12. Revisit EmbedSocial / Curator only if posting cadence spikes**
If you ever hit 3+ posts/week sustained and the 30-second CMS paste becomes a chore, reconsider the widget path. Spec note in `reels_embed_spec.md §1` has the trade-off table.

---

## Known gotchas (from the spec — worth repeating)

- **TikTok embed URL is NOT the same as post URL.** Use `/embed/v2/{VIDEO_ID}` pattern, not the canonical `/@user/video/...`.
- **Instagram's `/embed` path works without their JS widget** — this is why we avoid `embed.js`. Some "tutorials" will tell you to load it; don't.
- **Iframes need `loading="lazy"`** even with our IntersectionObserver — browser-native lazy is an extra safety net.
- **`aspect-ratio` CSS** on the iframe wrapper prevents CLS. Set it on `.reel__placeholder` so the card is the right height even before the embed loads.
- **Private/deleted posts:** the embed will render empty or error. The fallback copy + deeplink handles this — but if a post gets deleted on the platform, toggle `hide-from-homepage` in the CMS rather than leaving a broken card.

---

## What's blocked / what's unblocked

- **Unblocked now:** Phase 1 can start today. All specs are in place, staging is live, MCP access works.
- **Blocked on Steve:** picking the 6 initial reels and writing caption snippets. No one else can.
- **Blocked on nothing else:** no API tokens, no approvals, no copy writes needed.

---

## Definition of done

- ✅ 6 real reels on the homepage, driven by CMS.
- ✅ Embeds lazy-load (no third-party JS on initial page load).
- ✅ Adding a new reel is a 30-second CMS paste by Steve.
- ✅ Lighthouse performance score within 5 points of current staging.
- ✅ Posting log routine documented so this doesn't decay.
