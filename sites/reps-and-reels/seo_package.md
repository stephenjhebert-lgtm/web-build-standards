# Reps & Reels — SEO Package
**Stream A4 deliverable** — 2026-04-19
**Target:** rank #1 for "Reps and Reels" and "Reps & Reels" as branded searches within 30 days of relaunch. No competitive keyword chasing.

---

## 1. Title tag

```
Reps & Reels — A piper and a lifter. One feed.
```

**Character count:** 50 (under the 60 Google truncates at).
**Why:** the brand name first anchors the branded search target. The descriptor line matches the hero copy's voice without quoting it verbatim.

---

## 2. Meta description

```
Two hobbies nobody asked me to combine — competitive lifting, competitive piping, and the overlap. Instagram and TikTok @repsreels.
```

**Character count:** 140 (under the 155-160 Google truncates at).
**Why:** leads with the voice (the hero H1), names the two disciplines for disambiguation, signposts the social handles for direct traffic.

---

## 3. H1 (page)

```
Two hobbies nobody asked me to combine.
```

**Why:** exactly one `<h1>` per page. Matches the locked hero. No H1 drift between copy, meta, and markup.

---

## 4. Person schema (JSON-LD)

Paste into the `<head>` of the homepage as a `<script type="application/ld+json">` block.

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Steve Hebert",
  "alternateName": "Reps & Reels",
  "url": "https://www.repsreels.com",
  "image": "https://www.repsreels.com/og-image.png",
  "jobTitle": "Creator",
  "description": "Competitive lifter and bagpiper posting the overlap on Instagram and TikTok.",
  "sameAs": [
    "https://www.instagram.com/repsreels",
    "https://www.tiktok.com/@repsreels"
  ]
}
```

**Notes:**
- `alternateName` mirrors the brand so a search for either "Steve Hebert" or "Reps & Reels" resolves to the same entity in Google's Knowledge Graph.
- `sameAs` is the critical field — this is how Google links the entity to the social profiles. If you have a YouTube, LinkedIn-for-R&R, or other profile you want claimed, add it here. **Do not add Steve's personal/professional LinkedIn** — the brand hierarchy explicitly keeps LinkedIn separate.
- `image` points to the OG image. Needs to be absolute URL.

---

## 5. OG / Twitter tags (paste into `<head>`)

```html
<!-- Essential Open Graph -->
<meta property="og:type" content="website" />
<meta property="og:site_name" content="Reps & Reels" />
<meta property="og:title" content="Two hobbies nobody asked me to combine." />
<meta property="og:description" content="Competitive lifting, competitive piping, and the overlap. Instagram and TikTok @repsreels." />
<meta property="og:url" content="https://www.repsreels.com" />
<meta property="og:image" content="https://www.repsreels.com/og-image.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="Reps & Reels — Two hobbies nobody asked me to combine." />

<!-- Twitter/X Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="Two hobbies nobody asked me to combine." />
<meta name="twitter:description" content="Competitive lifting, competitive piping, and the overlap. @repsreels on IG and TikTok." />
<meta name="twitter:image" content="https://www.repsreels.com/og-image.png" />
```

**Test URLs after launch:**
- Facebook: https://developers.facebook.com/tools/debug/
- X/Twitter: https://cards-dev.twitter.com/validator (still works for cached checks)
- LinkedIn: https://www.linkedin.com/post-inspector/
- iMessage: send the link to yourself via iMessage and confirm the preview renders correctly
- Slack: paste in a DM to yourself

---

## 6. Canonical URL

**CRITICAL fix:** current live site has `<link rel="canonical" href="https://www.repsandreels.com" />` which is broken (typo — domain is `repsreels`, not `repsandreels`). Self-referencing canonical should be:

```html
<link rel="canonical" href="https://www.repsreels.com/" />
```

Trailing slash matches. If the site uses non-www or www, pick one and 301 redirect the other.

---

## 7. Favicon / icon markup

```html
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32.png" />
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16.png" />
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" />
<link rel="manifest" href="/site.webmanifest" />
<meta name="theme-color" content="#0B0B0C" />
```

**`site.webmanifest` content:**
```json
{
  "name": "Reps & Reels",
  "short_name": "R&R",
  "icons": [
    { "src": "/android-chrome-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "/android-chrome-512.png", "sizes": "512x512", "type": "image/png" }
  ],
  "theme_color": "#0B0B0C",
  "background_color": "#0B0B0C",
  "display": "standalone"
}
```

---

## 8. `robots.txt`

```
User-agent: *
Allow: /

Sitemap: https://www.repsreels.com/sitemap.xml
```

**Notes:** no disallows needed. The site is public and wants to be crawled. Webflow auto-generates `/sitemap.xml` — confirm it's present after launch.

---

## 9. Sitemap

Webflow handles this automatically at `/sitemap.xml`. Post-launch actions:
1. Submit to Google Search Console: https://search.google.com/search-console
2. Submit to Bing Webmaster Tools: https://www.bing.com/webmasters
3. Request indexing for the homepage specifically in GSC.

---

## 10. Secondary (branded-combo) keywords

No effort required — these will resolve naturally once the site is indexed with the schema above.

- "Reps and Reels" → branded, #1 target
- "Reps & Reels" → branded, #1 target
- "Steve Hebert Reps Reels" → branded combo, secondary
- "repsreels" → branded handle, secondary
- "Reps Reels bagpipes" → disambiguation for people who remember half the name
- "Reps Reels lifting" → same

**What we are NOT targeting:**
- "bagpipes Instagram"
- "fitness creator"
- "hybrid athlete"
- "bodybuilding content creator"
- "bagpipe lessons"

These are competitive keyword quicksand. The brand voice guide and project review both explicitly say: don't chase growth keywords. Phase 1 is consistency and signal. The site ranks for branded search and that's the scope.

---

## 11. Analytics recommendation

**Recommend: Plausible Analytics** ($9/mo, lightweight, privacy-first, EU-hosted, one-line script, Lighthouse-friendly).

**Why not GA4:**
- Heavy JS footprint (hurts the Lighthouse target)
- Consent banner legal overhead (even in US, increasingly expected)
- The depth of GA4 is wasted on this site — there are no funnels to measure
- Plausible's dashboard is readable at a glance, which matches Steve's preference for low-friction tools

**Alternative if budget is zero:** Cloudflare Web Analytics (free, zero-config if the site is behind Cloudflare). Less detail than Plausible but good enough for a placeholder site.

**Do not use:** Mixpanel, Amplitude, Heap — overkill. Hotjar — session replay is irrelevant here.

### Plausible install (once account exists)
```html
<script defer data-domain="repsreels.com" src="https://plausible.io/js/script.js"></script>
```

Add to Webflow's custom code in site settings, not per-page.

---

## 12. 30-day ranking checklist

To hit "rank #1 for 'Reps and Reels' within 30 days":

- [ ] Launch site with all schema, meta, OG, canonical correct on day 0
- [ ] Submit to Google Search Console within 24 hours
- [ ] Submit to Bing Webmaster Tools within 24 hours
- [ ] Request indexing of homepage in GSC (day 1)
- [ ] Post a link to the site on @repsreels IG bio and TikTok bio (day 1)
- [ ] Share the link in one IG story and one TikTok post (day 2-3, natural-looking, not an announcement)
- [ ] Confirm IG and TikTok profiles link back to repsreels.com (builds the `sameAs` signal)
- [ ] Check weekly for 4 weeks: search "Reps and Reels" incognito; confirm rank position
- [ ] If not #1 by day 21, diagnose: GSC coverage issues, canonical drift, thin-content flags

**Realistic expectation:** branded rank #1 is very achievable. The current live site already ranks for "Reps and Reels" (confirmed by it being the authoritative domain for that exact brand name). Relaunching with proper schema accelerates, doesn't threaten, the rank.

---

## 13. Questions / decisions for Steve

1. **YouTube / LinkedIn-for-R&R profile** — do you want any additional social links added to the Person schema `sameAs` array? Currently just IG + TikTok. Leave blank if no.
2. **Plausible vs. Cloudflare vs. something else for analytics** — recommendation is Plausible. Confirm or tell me to stand up Cloudflare instead.
3. **Domain: www or non-www?** — current canonical has www. Brief doesn't specify. Recommend keeping www (matches current site, avoids DNS rework). Confirm.

Defaulting to recommendations unless flagged.
