# Reps & Reels — Launch Checklist
**COMPLETED: 2026-04-22** — Site is live at `repsreels.com`.

> **Post-launch notes:** CMS plan (Basic) required static hardcoded embeds instead of CMS Collections. See `POSTMORTEMS.md` (2026-04-22 entries) and `KEY_INSIGHTS.md` for Webflow MCP workarounds documented during launch. Several Phase 2 QA items and Phase 4 post-launch items remain to be completed.

---


## 0. Phases at a glance

```
Phase 1 — Build (Webflow, in staging)         ~3-5 hrs
Phase 2 — Pre-launch QA                       ~1 hr
Phase 3 — Go live (DNS cutover)               ~15 min of action, up to 48hr propagation
Phase 4 — Post-launch (indexing, monitoring)  ~30 min over first week
```

Total hands-on time: **~5-7 hours of active work.** Most of it is Phase 1.

---

## Phase 1 — Build the site in Webflow staging

**Goal:** rebuild the approved prototype in Webflow, using the existing project so the current live site's URL stays intact during the cutover.

### 1.1 Pre-build prep

- [ ] Duplicate the current Webflow project as `repsreels-v2` backup. If anything goes sideways during the rebuild, you can restore from this duplicate.
- [ ] Confirm Webflow plan supports CMS (Basic does not — needs CMS, Business, or above). If not on a CMS plan, upgrade before building the Reels Collection.
- [ ] Export the current site's settings: SEO meta, redirects, canonical. Screenshot the Project Settings page.

### 1.2 Strip the current site down

Working in the current Webflow project (not the duplicate — that's the backup), remove:

- [ ] Dark mode toggle (entire component + its JS)
- [ ] FAQ section (per brief, out of scope for MVP)
- [ ] Any current hero content — replacing wholesale
- [ ] Current Fustat-only font setup — keeping Fustat for body, adding Big Shoulders Display and IBM Plex Mono

### 1.3 Fonts (Webflow → Project Settings → Fonts)

- [ ] Add **Big Shoulders Display** (Google Fonts) — weights 800, 900
- [ ] Keep **Fustat** (already installed) — weights 400, 500, 600
- [ ] Add **IBM Plex Mono** (Google Fonts) — weights 500, 600
- [ ] Configure `font-display: swap` (Webflow does this by default for Google Fonts)

### 1.4 Color variables (Webflow → Style Guide → Color Swatches)

Create global swatches:

- [ ] `Forge Black` — `#0B0B0C`
- [ ] `Aged Brass` — `#C9A24B`
- [ ] `Bone Paper` — `#F1ECE1`
- [ ] `Oxblood` — `#6B1E1E`
- [ ] `Iron Grey` — `#3A3A3D`
- [ ] `Paper` — `#E8E4DB`

### 1.5 Build the sections

In order (top to bottom of page):

- [ ] **Nav** — round patch logo (28px) + "Reps & Reels" wordmark (left), IG + TikTok icons (right). Social icons inline SVG, not images.
- [ ] **Hero** — 100vh, Forge Black bg, `brand_mark_primary_gold.png` as background element at 25-35% opacity with radial gradient mask, mono chip (`RR · THE OVERLAP`), H1 ("Two hobbies nobody asked me to combine."), subhead. No scroll chevron.
- [ ] **Voice moment** — one paragraph + the cornerstone pull-quote ("The years are invisible. The result isn't.") with aged-brass left border and mono type.
- [ ] **Reels grid** — see §1.6 below (this is the complex one)
- [ ] **About / origin** — Bone Paper bg break, single paragraph max 32ch wide, ends on "Most weeks."
- [ ] **Contact** — "Hit me." heading, mailto link with left-to-right underline scrub hover, Omnisend form embed placeholder framed with "Occasional email. Not a newsletter exactly."
- [ ] **Footer** — full manifesto mark (140-160px) on left, "Not an expert. Just consistent." tagline, social icons (48px tap targets), hello@repsreels.com, "© 2026. All reps reserved."

### 1.6 Reels grid (Stream B2 deliverable)

Follow `reels_embed_spec.md` §2-§8 exactly. Summary of sub-tasks:

- [ ] Create **Reels** CMS Collection with 7 fields (see B2 §2)
- [ ] Add 6 placeholder Reels using real IG/TikTok post URLs so you're designing against real data
- [ ] Place a Collection List in the reels section, bind to Reels Collection, sort/limit/filter per B2 §2
- [ ] Add Embed block inside each Collection Item using the template from B2 §5
- [ ] Paste JS into Webflow → Project Settings → Custom Code → Footer Code (B2 §6)
- [ ] Paste CSS into Webflow → Project Settings → Custom Code → Head Code (B2 §7)
- [ ] Apply `.reels-grid` class to the Collection List wrapper (B2 §8)

### 1.7 Omnisend form

- [ ] Copy the existing Omnisend embed code from the current site's form element (Webflow Designer → right-click the form → Copy code, OR pull from Omnisend → Forms → Embed)
- [ ] Paste into an Embed block in the Contact section, where the prototype has the placeholder div
- [ ] Style overrides — Omnisend forms ship with their own CSS. Add a scoped `<style>` block inside the Embed to match Forge Black bg, Paper text, Aged Brass button. Test at mobile width — their inputs can get weird on small screens.
- [ ] Confirm the form still submits into the same Omnisend list it currently does (don't rewire the ESP side during a site launch)

### 1.8 Head tags (Webflow → Project Settings → Custom Code → Head Code + Page Settings → SEO)

From `seo_package.md`:

- [ ] **Title tag** (Page Settings → SEO → Title Tag): `Reps & Reels — A piper and a lifter. One feed.`
- [ ] **Meta description** (Page Settings → SEO → Meta Description): `Two hobbies nobody asked me to combine — competitive lifting, competitive piping, and the overlap. Instagram and TikTok @repsreels.`
- [ ] **Canonical** — Webflow auto-generates. Confirm it resolves to `https://www.repsreels.com/` with trailing slash after launch. **CRITICAL:** the current site has a broken canonical pointing at `repsandreels.com` (typo). Verify the rebuild doesn't inherit this.
- [ ] **OG tags + Twitter card** — Webflow's SEO panel handles `og:title`, `og:description`, `og:image`. Add the rest (og:type, og:url, og:site_name, twitter:card) via custom head code if Webflow's panel doesn't expose them.
- [ ] **OG image** — 1200×630 PNG. Upload to Webflow Assets, get the absolute URL, reference in OG tags. **Do not ship with a placeholder** — image_specs.md §6 is explicit: don't leave OG to AI generation, design it manually in Figma/Canva. This is a blocking launch item because it determines what shows in iMessage, Slack, and LinkedIn previews. Spec:
  - 1200×630 PNG, under 200KB
  - Forge Black bg (`#0B0B0C`)
  - Big Shoulders Display 900 H1 in Aged Brass (`#C9A24B`) — "TWO HOBBIES NOBODY ASKED ME TO COMBINE."
  - Manifesto mark at 50% opacity, right third
  - IBM Plex Mono tagline bottom-right: "REPSREELS.COM"
- [ ] After OG image is uploaded, **update the `Person` JSON-LD schema's `image` field** to the real Webflow asset URL (currently references `og-image.png` as a placeholder). Update OG tags' `og:image` and `twitter:image` to the same URL.
- [ ] **Person schema (JSON-LD)** — paste the full `<script type="application/ld+json">` block from seo_package.md §4 into Custom Code → Head Code.
- [ ] **Favicon** — upload `rr_sticker.png` (or a 32×32 export) via Webflow → Project Settings → General → Favicon. Apple touch icon from `brand_mark_primary_gold.png`.
- [ ] **theme-color meta** — `<meta name="theme-color" content="#0B0B0C" />` in head code.

### 1.9 Analytics

- [ ] Create a Plausible account (or use existing). Add `repsreels.com` as a site.
- [ ] Paste the Plausible script into Webflow → Project Settings → Custom Code → Head Code:
  ```html
  <script defer data-domain="repsreels.com" src="https://plausible.io/js/script.js"></script>
  ```
- [ ] **Do NOT** install GA4 concurrently. Plausible is the one tool; no double-tracking bloat.
- [ ] Remove any analytics scripts from the current site before launch (check head code + footer code).

### 1.10 Strip old stuff

- [ ] Remove the old FAQ page/section entirely
- [ ] Remove the dark/light mode toggle JS
- [ ] Remove any scripts from the current site that aren't in the new stack (old analytics, old font loaders, old jQuery if any)
- [ ] Confirm total custom JS is under 15KB minified (the B2 reel loader is ~2KB, Plausible is external, Omnisend is ~40KB — Plausible + Omnisend + B2 loader ≈ your entire budget, and Plausible doesn't count toward same-origin)

---

## Phase 2 — Pre-launch QA (run in Webflow staging, not production)

### 2.1 Visual / content QA

- [ ] Every section renders correctly at **375px** (iPhone SE), **390px** (iPhone 14), **768px** (iPad portrait), **1280px** (laptop), **1920px** (desktop wide)
- [ ] H1 doesn't wrap awkwardly at any breakpoint
- [ ] Hero brand mark background visible but subtle at all breakpoints (25-35% opacity)
- [ ] Footer manifesto mark doesn't dominate on mobile (~40vw max)
- [ ] Bone Paper section transition (about section) feels intentional, not accidental
- [ ] All 6 reel cards render with real content, not placeholder text
- [ ] No Lorem Ipsum anywhere. Grep the whole site.

### 2.2 Functional QA

- [ ] Click all 6 social icons — confirm destination URLs are correct
- [ ] Click mailto link — confirm it opens mail client with `hello@repsreels.com` prefilled
- [ ] Submit the Omnisend form with a test email — confirm it lands in the correct Omnisend list
- [ ] Tap each reel card — confirm embed loads (or fallback appears)
- [ ] Scroll past reel cards — confirm IntersectionObserver auto-loads them on viewport entry
- [ ] Kill wifi mid-load on a reel — confirm "Embed's not cooperating" fallback appears within 8 seconds
- [ ] Set one CMS reel's Post URL to deliberately broken (`https://instagram.com/reel/FAKE/`) — republish — confirm fallback renders
- [ ] `prefers-reduced-motion: reduce` (Mac: System Settings → Accessibility → Display → Reduce motion) — confirm no hover transforms, no fadeUp animation on hero
- [ ] Keyboard-only navigation: tab through entire page — confirm focus outlines visible (2px Aged Brass), all interactive elements reachable, skip link works

### 2.3 Performance (Lighthouse on mobile, incognito, throttled 4G)

Run: Chrome DevTools → Lighthouse → Mobile → Performance + Accessibility + SEO + Best Practices

- [ ] **Performance ≥ 85** (target 90+)
- [ ] **LCP < 2.5s**
- [ ] **CLS < 0.1** — reserve space for every reel card with `aspect-ratio: 9/16`
- [ ] **TBT < 300ms**
- [ ] **Accessibility = 100** (or ≥95 — fix anything Lighthouse flags)
- [ ] **SEO = 100**
- [ ] **Best Practices ≥ 95**

If Performance is below 85:
- Check Network tab: is Omnisend's JS the biggest chunk? Load it with `defer` attribute.
- Check that reel iframes aren't firing on initial load (they shouldn't — if they are, the JS from B2 §6 didn't paste correctly)
- Check that the hero brand mark PNG isn't over-sized — should be served ≤200KB via Webflow's built-in optimization

### 2.4 SEO validation

- [ ] **Rich Results Test:** paste staging URL into https://search.google.com/test/rich-results — confirm Person schema validates with no errors
- [ ] **Mobile-Friendly Test:** https://search.google.com/test/mobile-friendly — confirm passes
- [ ] **OG preview test (iMessage):** send the staging URL to yourself in iMessage. Confirm card renders with correct title, description, image.
- [ ] **OG preview test (Slack):** paste staging URL in a Slack DM to yourself. Confirm unfurl renders correctly.
- [ ] **OG preview test (LinkedIn):** https://www.linkedin.com/post-inspector/ — paste URL, confirm preview is right.
- [ ] View source on the staging URL — confirm:
  - Exactly one `<h1>`
  - Canonical tag present and correct
  - Person JSON-LD script present
  - All meta tags present (og:, twitter:, theme-color)
- [ ] **Canonical typo check (critical, easy to miss):** `curl -s https://<staging-url> | grep -i canonical` (or view-source + Cmd+F "canonical"). Confirm output shows `https://www.repsreels.com/` — NOT `repsandreels.com`. The current live site has this typo; the rebuild must not inherit it. Repeat this exact check on production after Phase 3.3.

### 2.5 Accessibility deeper check

- [ ] Run **axe DevTools** (Chrome extension) on staging URL — fix any violations
- [ ] Contrast check with a real tool: Paper (#E8E4DB) on Forge Black (#0B0B0C) = 15.3:1 ✅ / Aged Brass (#C9A24B) on Forge Black = 9.6:1 ✅
- [ ] VoiceOver test (Mac: Cmd+F5) — navigate top to bottom. Confirm: nav announces as nav, hero H1 announced, reel cards announce platform + caption, mailto link announces, footer reads as footer.
- [ ] Tap target check: every interactive element ≥48px on mobile. Use Chrome DevTools device toolbar → inspect each icon and link.

---

## Phase 3 — Go live

**Pre-flight:** do not touch DNS until Phase 2 is 100% clean. If anything above fails, fix and re-test.

### 3.1 Backup the current live site

- [ ] Webflow → Current Project → Backups → Create manual backup, label it `Pre-v2-launch 2026-XX-XX`. This is the rollback point.

### 3.2 Pre-launch DNS check

Per the brief's non-negotiable: **do not touch DNS without explicit user confirmation.** This section describes what needs to happen, but the user triggers each action.

- [ ] Confirm current DNS registrar (likely Squarespace/Google Domains/Namecheap — whoever owns `repsreels.com`)
- [ ] Note the current A records and CNAME records pointing at Webflow (Webflow's recommended records: `A` → `75.2.60.5` and `A` → `99.83.190.102` for apex, `CNAME` → `proxy-ssl.webflow.com` for www) — verify these are what's currently set
- [ ] Confirm whether the site uses `www.repsreels.com` or apex `repsreels.com` as primary. Per SEO package, recommending `www` stays primary; apex 301-redirects to www.

### 3.3 Publish to production

- [ ] In Webflow → Project → Publish tab → Publish to `www.repsreels.com` AND the webflow.io staging URL. This is the go-live moment.
- [ ] Within 60 seconds, load `https://www.repsreels.com` in incognito — confirm the new site appears. DNS is already pointing at Webflow, so no DNS change needed if you're using the same Webflow project.
- [ ] Hard refresh (Cmd+Shift+R) — confirm no stale cache issues.

**Note:** since we're keeping the existing Webflow project (not migrating to a new host), there's no actual DNS change for this launch. The "go-live" is the Webflow Publish action. If at any point you decide to move hosts, DNS becomes real work — and you'd pause for explicit confirmation per the brief.

### 3.4 Immediate post-publish checks

Within 5 minutes of publish:

- [ ] Load `https://repsreels.com` (no www) — confirm 301 redirect to `https://www.repsreels.com`
- [ ] Load `https://www.repsreels.com` — confirm new site loads with HTTPS (Webflow handles SSL automatically)
- [ ] View source — confirm canonical points at `https://www.repsreels.com/` (not repsandreels.com)
- [ ] Resubmit OG preview tests (§2.4) on the production URL — iMessage, Slack, LinkedIn
- [ ] Submit the URL to the Facebook debugger: https://developers.facebook.com/tools/debug/ — this forces Meta to re-scrape your OG tags. Otherwise iMessage/FB caches can show stale previews for days.
- [ ] Load on your actual phone (not just DevTools emulator) — scroll the whole page, tap every link

---

## Phase 4 — Post-launch (first 24-72 hours)

### 4.1 Submit to search engines

- [ ] **Google Search Console:** https://search.google.com/search-console → add `www.repsreels.com` as a property → verify via Webflow meta tag or DNS TXT → submit sitemap at `https://www.repsreels.com/sitemap.xml`
- [ ] **Request indexing** for the homepage specifically in GSC: URL Inspection tool → paste homepage URL → Request Indexing
- [ ] **Bing Webmaster Tools:** https://www.bing.com/webmasters → add site → submit sitemap
- [ ] Confirm `robots.txt` resolves at `https://www.repsreels.com/robots.txt` and lists the sitemap

### 4.2 Social profile linkback audit

Per the SEO package's `sameAs` schema signal:

- [ ] **Instagram bio:** update link to `https://www.repsreels.com`
- [ ] **TikTok bio:** update link to `https://www.repsreels.com`
- [ ] Post one story on each platform with the site link, casual framing (not "new site announcement" — per brand voice, no announcing)

### 4.3 Monitoring

Over first 2 weeks:

- [ ] **Day 1:** Plausible dashboard — confirm pageview events are firing
- [ ] **Day 3:** Search `"Reps and Reels"` incognito — note current rank. Current live site likely already ranks #1; rebuild shouldn't regress.
- [ ] **Day 7:** GSC → Coverage report — confirm homepage is indexed, no errors flagged
- [ ] **Day 7:** GSC → Enhancement reports — confirm Person schema is picked up (may take longer)
- [ ] **Day 14:** re-run Lighthouse on production — confirm scores haven't drifted (sometimes Omnisend pushes updates that balloon their script size)
- [ ] **Day 21:** if branded search for `"Reps and Reels"` isn't resolving to repsreels.com at #1, diagnose: GSC coverage issue, canonical still broken, cache somewhere in the CDN chain

### 4.4 Housekeeping

- [ ] Delete the `repsreels-v2 backup` Webflow project duplicate once the new site is stable for 14 days (don't delete earlier — it's your rollback)
- [ ] Archive the old prototype files in `/Reps and Reels/web/` — they're approval artifacts, keep them as documentation

---

## Rollback plan

If something breaks post-launch and Phase 3.4 checks fail:

1. Webflow → Project → Backups → restore `Pre-v2-launch` backup → republish
2. The restore takes ~30 seconds. Site is back to the prior state immediately.
3. Document what broke in the backup folder: `/web/launch_postmortem_YYYY-MM-DD.md`
4. Fix in staging, re-run Phase 2, try launch again when clean.

**Hard rollback trigger conditions:**
- Any 5xx error on the homepage
- Person schema fails validation
- Canonical resolves to wrong domain
- Omnisend form stops accepting submissions
- Any reel card leaves a broken iframe visible with no fallback

---

## Deferred / Phase 2 work

These are explicitly out of scope for launch but worth logging:

- **Proper OG image** — design in Figma or build as HTML render, export 1200×630 PNG. Currently using `brand_mark_primary_gold.png` as launch placeholder.
- **Dedicated `/reels` sub-page** — Collection Page template, unfiltered reel list. Deferred until there's a reason.
- **Favicon SVG + full icon set** — currently using `rr_sticker.png` at 32×32 which works but isn't optimal. Proper PWA manifest icons deferred.
- **Structured data beyond Person** — could add `CreativeWork` entries for individual reels at some point. Not needed for launch.
- **Webflow CMS migration from manual reel paste to automated sync** — would need Zapier/Make + IG Graph API + TikTok API. Real money, real maintenance. Defer until post volume justifies.

---

## Monetization lens check

Per the operating directive: is any of this sellable as a capability?

**Yes — selectively.** The reels embed spec (B2) is the most portable IP from this project. The pattern — native IG + TikTok embeds with lazy-load and branded fallbacks, in a Webflow CMS — is something most DTC / creator brands want and don't know how to spec. It's a 1-2 hour consulting deliverable at Junk Gym Digital pricing, packaged as "Creator feed integration for Webflow."

**Not sellable:** the brand voice guide, the design direction doc, and the homepage copy. Those are intrinsic to this brand.

**Flagging for later:** if the reels embed spec gets polished post-launch, it could become a JGD lightweight service offering. Stage, don't build now.

---

## Sign-off

Before going live, one final check: read the full homepage, out loud, on mobile. If any line reads corny, AI-generated, or off-brand — revise before publishing. The voice guide is the final gate. If it passes the "would Steve say this" test in his own voice, it ships.
