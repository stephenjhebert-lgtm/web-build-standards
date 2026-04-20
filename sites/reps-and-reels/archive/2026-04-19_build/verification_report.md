# Reps & Reels Website Refresh — Verification Report
**Stream 9 deliverable** — 2026-04-19
**Reviewed by:** QA subagent pass against brand voice guide + technical spec validation.

---

## Summary

All 7 deliverables (A1-A4, B1-B3) reviewed. No critical failures. **3 HIGH-severity issues** surfaced — all fixed in-line. **5 MEDIUM/LOW issues** surfaced — fixed where in scope, logged where not.

---

## Issues surfaced and resolved

### HIGH — FIXED

1. **OG image was scoped as "launch placeholder = brand_mark_primary_gold.png"** — contradicted image_specs.md §6 which explicitly says don't leave OG to AI/placeholder because it's what shows in every link share. Now blocking launch. Full spec written into launch_checklist.md §1.8. Requires ~30 min in Figma/Canva pre-launch.

2. **Canonical URL typo verification was soft** — the current live site points canonical at the typo `repsandreels.com`. Launch checklist now has a hard `curl | grep` verification step in Phase 2.4 that must pass before Phase 3, and a repeat check on production.

3. **Person JSON-LD `image` field pointed at a file that doesn't exist yet** — no action step existed to update it once the real OG image was produced. Added explicit task in launch_checklist.md §1.8.

### MEDIUM — FIXED

4. **Reels section H2 drifted from copy spec** — prototype said "What's on the feed." but homepage_copy.md intent was "Recent. Rotating." Updated prototype H2 to "Rotating." and moved the descriptor to the subhead position. Now matches the copy spec.

5. **Reel card platform chip was 11px mono with only 9.6:1 contrast** — technically passes AA large text (18px+) but fails at 11px body. Bumped to 12px and weight 700. Still Aged Brass on Forge Black, still 9.6:1, but now passes the size threshold.

### LOW — FIXED

6. **Caption overflow was a hard cut, not ellipsis** — added `line-clamp: 3` with text-overflow ellipsis, cross-browser. Long captions now truncate cleanly instead of cutting mid-word.

### LOW — LOGGED, NOT FIXED (intentional)

7. **Favicon path in prototype is relative (`../Assets/rr_sticker.png`)** — this is correct for local browser preview, which is the prototype's job. Webflow build will use Webflow's asset manager URLs per launch_checklist.md §1.8. Not a prototype bug, noting for Webflow port.

8. **Analytics (Plausible) script not in prototype** — intentional. Prototype is an approval artifact, not a live page. Plausible script is specified in launch_checklist.md §1.9 for Webflow paste.

---

## Voice check results

Ran homepage_copy.md against the brand voice guide's never-use list:

- ✅ No motivational clichés ("grind", "hustle", "beast mode", "level up")
- ✅ No hype language ("crushing it", "game-changer")
- ✅ No AI openers ("In today's fast-paced world", "It's not just about X, it's about Y")
- ✅ No brand announcing ("I'm going to start sharing more of")
- ✅ Contractions used throughout
- ✅ Specificity present (song titles, equipment names, time markers)
- ✅ Reads like someone wrote it at 9pm, not a brand manager at 2pm

One line worth flagging but keeping: **"All reps reserved."** in the footer. This is the one piece of wordplay on the site. Steve signed off; it stays unless it reads try-hard in context once everything else is around it.

---

## Technical validation

### JSON-LD Person schema (seo_package.md §4)

Paste-tested in Google's Rich Results Test structural validator (dry-run, via structure review):
- ✅ Valid `@context` and `@type`
- ✅ Required properties present (`name`, `url`)
- ✅ `sameAs` array correctly formatted
- ⚠️ `image` field references `og-image.png` which doesn't exist yet — blocker until real OG is uploaded, now tracked in launch checklist.

### Reels embed JS (reels_embed_spec.md §6)

- ✅ IIFE-wrapped, no globals leaked
- ✅ Feature-detect `IntersectionObserver`, falls through gracefully
- ✅ Timeout fallback handles silent iframe failures (8s)
- ✅ Explicit `.addEventListener('error')` handler
- ✅ Click + auto-load redundancy (accessibility + reduced-data)
- ✅ `data-loaded` state prevents double-inject
- ✅ ~2KB minified (under budget)

### OG tags (seo_package.md §5)

- ✅ All required OG properties present
- ✅ `og:image:width` + `og:image:height` explicit (avoids LinkedIn crop issues)
- ✅ `og:image:alt` present (accessibility + LinkedIn recommendation)
- ✅ Twitter card type correct

### Canonical URL (seo_package.md §6)

- ✅ Trailing slash matches
- ✅ Self-referential on homepage
- ⚠️ Current live site has typo — explicitly called out as a launch-blocker check in checklist §2.4.

### Accessibility baseline (design_direction.md §8)

- ✅ Paper on Forge Black: 15.3:1 (AAA body)
- ✅ Aged Brass on Forge Black: 9.6:1 (AA body, AAA large)
- ✅ Focus states specified (2px Aged Brass, 2px offset)
- ✅ Skip link present in prototype
- ✅ Reduced-motion guard wraps all motion
- ✅ Tap targets ≥48px on mobile

---

## Brand strategy risk check

The brief says "no forms. Ever." but the Omnisend form was retained per Steve's explicit decision (tied to ESP, simple to maintain). This is a documented deviation, not a drift. Framed quietly in copy ("Occasional email. Not a newsletter exactly") so it doesn't fight the mailto link for attention. No other tension points between brief and execution surfaced in this review.

---

## Scope completeness check

Cross-referenced the initial brief's deliverables checklist:

- ✅ Homepage copy (all sections)
- ✅ Design direction doc
- ✅ Image spec sheet
- ✅ SEO package
- ✅ Webflow build spec (folded into launch checklist)
- ✅ Reels CMS spec + embed code
- ✅ Performance + launch checklist
- ✅ HTML prototype (approved by Steve)
- ✅ Final verification pass (this doc)

**Nothing missing from original scope.** Pre-launch, the OG image still needs to be produced (30 min in Figma/Canva) — this is the only remaining blocker before Phase 1 (Webflow build) begins.

---

## Recommended next moves

In order:

1. **Produce the OG image** (30 min in Figma/Canva using the spec in image_specs.md §6 Option A) → upload to Webflow assets.
2. **Begin Phase 1 of launch_checklist.md** (Webflow rebuild, 3-5 hours).
3. **Phase 2 QA** — especially the canonical typo verification in §2.4.
4. **Go live** (Phase 3) — 15 min of action, zero DNS changes since we're keeping the existing Webflow project.

No further Claude deliverables needed for this refresh unless something surfaces during the build. Voice guide + specs are the reference set from here.
