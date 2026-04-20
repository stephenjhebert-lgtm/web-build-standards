# Staging QA — Round 7 Fix Pass
**URL:** https://reps-reels-320557.webflow.io/?cb=r7verify
**Date:** 2026-04-19
**Verdict:** ✅ MVP blockers cleared. Staging is viewable end-to-end with real copy.

Published to Webflow subdomain only. No production publish. No DNS changes.

---

## What round 6 actually shipped vs. what it claimed

Round 6 build log claimed "full pass." DOM inspection told a different story:

- 23 `"This is some text inside of a div block."` placeholder strings across nav wordmark, nav socials, hero chip, reels label, 6 reel platform chips, 6 reel embed blocks, about label, contact label, contact form placeholder, footer tagline, footer socials.
- Contact section had the email duplicated — once inside the body paragraph, again as a standalone link directly below.
- Font loader only included Fustat; Big Shoulders Display and IBM Plex Mono weren't being requested at runtime, so the display and mono typography fell back.
- Canonical URL in head is `https://www.repsandreels.com` — a typo domain (should be `repsreels.com`).
- Stale FAQPage JSON-LD from the Evolux template is still injected via Site Settings → Custom Code → Head.

All class bindings (`nav`, `hero`, `footer`, `container`, all BEM children) were correct. The structural skeleton is solid — the content layer just never got populated.

## What got fixed in round 7 (via MCP)

| Item | How | Verification |
|---|---|---|
| 23 placeholder strings → real copy | `element_tool.set_text` directly on the String-type text nodes (the Block parents reject `set_text`; the String children accept it) | `placeholder_strings_remaining: 0` |
| Nav wordmark / hero chip | Both now read "Reps & Reels" | 14 occurrences of "Reps & Reels" across DOM + schema |
| 6 reel cards | IG/TT/IG/IG/TT/IG platform chips + "[ Reel embed ]" placeholder per prototype | `reel_embed_text: 6`, `reel_cards: 6` |
| About/Contact labels | "Origin" / "Contact" | confirmed |
| Contact body duplicate email | Shortened body from "...saying hi — hello@repsreels.com." to "...saying hi." The dedicated `contact__email-link` below remains as the single clickable email. | paragraph + link are now non-redundant |
| Font loader | Registered `rr_font_loader` inline header script that appends a Google Fonts stylesheet link for `Big Shoulders Display:800,900` + `IBM Plex Mono:500,600`, plus the two `preconnect` hints. Applied site-wide. | `big_shoulders_ref: 2`, `ibm_plex_mono_ref: 2`, `font_loader_script: 1` |
| Footer tagline | "Not an expert. Just consistent." | confirmed |

### Verification sweep (post-publish)

```json
{
  "placeholder_strings_remaining": 0,
  "reps_reels_text": 14,
  "reel_embed_text": 6,
  "omnisend_placeholder": 1,
  "big_shoulders_ref": 2,
  "ibm_plex_mono_ref": 2,
  "font_loader_script": 1,
  "nav_class": 1,
  "hero_class": 1,
  "footer_class": 1,
  "container_class": 7,
  "reel_cards": 6,
  "hit_me": 1,
  "not_an_expert": 1,
  "rotating": 1,
  "person_jsonld": 1
}
```

All collision classes from round 6 (`nav`, `hero`, `footer`, `container`) still bind correctly and are non-zero. BEM bindings survive.

## Steve-manual items (non-blocking for staging MVP, but needed before prod)

These live in Webflow Site Settings → Custom Code → Head. MCP can register inline scripts but cannot edit the raw head paste that round 3 dropped in.

1. **Canonical URL typo.** Head currently contains `<link rel="canonical" href="https://www.repsandreels.com">`. That domain doesn't exist — correct is `https://repsreels.com`. 3 occurrences in the current HTML.
2. **Stale FAQPage JSON-LD.** The old Evolux FAQ schema is still injected. The new `person_jsonld_schema` registered script already injects a correct Person schema — the stale block needs to be removed from Site Settings head so schema.org doesn't see two incompatible documents. 1 occurrence.
3. **OG image is `RnReels.jpg`.** The asset name matches the brand ("RnReels" = "Reps & Reels") and it's a valid PNG/JPG — functionally fine for staging. If Steve wants a richer OG card, swap in Site Settings → SEO → Open Graph image. 2 occurrences are expected (one `og:image`, one `twitter:image`).
4. **Favicon.** `Favicon.png` is a simple mark. If a higher-fidelity favicon exists, swap in Site Settings → General → Favicon.
5. **Reel thumbnails (polish).** Assets `reel_01.jpg` through `reel_06.jpg` are uploaded but unused — the 6 reel cards currently show the `[ Reel embed ]` text placeholder. Binding the thumbnails would require adding Image children to each `reel__placeholder`; skipped for MVP per the brief's "no new components without approval." Can be added in the next polish pass.
6. **Template pages still empty.** 404/401/Licenses/Changelog render blank (from round 6). Leave or rebuild.
7. **Nav/hero/footer all reuse `RepsReels_Logo.png` (asset id `693224bc137c88a8368d8da8`).** This IS the brand logo — name-accurate. If the hero needs a wide banner asset vs. a square logo, upload a dedicated hero bg and swap via `set_image_asset` on the no-style hero image element `b064f89e-2d75-b979-4722-b80758acd19b`.

## Hard stops honored

- Published only to `reps-reels-320557.webflow.io` — no `customDomains`, no production publish.
- No DNS changes.
- No CMS structure touched.
- No Pascal-case classes reintroduced.
- No component rebuilds — all fixes surgical (set_text on existing text nodes, one new inline script, no new elements).

## Handoff

Staging is ready to show. Typography should now render with Big Shoulders Display on the H1 and IBM Plex Mono on the eyebrows/chips as soon as the browser fetches the font loader. Copy matches `homepage_copy.md`. Structure matches the prototype skeleton.

The canonical-URL typo and stale FAQPage schema are the only SEO-relevant items; both are 30-second edits in Site Settings when Steve is ready.
