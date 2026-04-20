# SEO Fixes — Staging Push Log
**Date:** 2026-04-20
**Staging URL:** https://reps-reels-320557.webflow.io
**Production:** NOT TOUCHED (still running 2025-12-09 publish)

---

## ✅ What landed on staging

### 1. Homepage SEO metadata (now live on staging)
| Field | Old (prod) | New (staging) |
|---|---|---|
| `<title>` | Reps & Reels — Bagpiping, Weight Training & Creator Journey | Reps & Reels — A piper and a lifter. One feed. |
| Meta description | "…grind behind both. Join the journey…" | "Two hobbies nobody asked me to combine — competitive lifting, competitive piping, and the overlap. Instagram and TikTok @repsreels." |
| OG title | Reps & Reels — Bagpipes, Training & Lifestyle | Two hobbies nobody asked me to combine. |
| OG description | "Where the barbell meets the bagpipe. Follow the journey…" | Competitive lifting, competitive piping, and the overlap. Instagram and TikTok @repsreels. |

**Verification:** `curl -s https://reps-reels-320557.webflow.io/` shows all four updated.

### 2. 404 page rebranded
Title: `Reps & Reels — Page not found` (was `Evolux – Page Not Found`)
Description: `That page isn't here. Head home — or find the feed on Instagram and TikTok @repsreels.`

### 3. Changelog + Licenses SEO cleaned
Both descriptions changed from "…for Evolux" → "Not in use."
(Archive/draft flags were not honored by the Webflow API for utility pages — need manual action, see below.)

### 4. Enhanced head-fix script registered (v1.1.0)
A new version of `rr_head_fix` is registered on the site. It:
- Fixes canonical URL from `repsandreels.com` → `https://www.repsreels.com/`
- **Keeps** FAQPage schema (v1.0.0 was destructively removing it — this was hurting AI answer-engine discoverability)
- Adds `knowsAbout` array to Person schema (11 topic tags including Bagpiping, Gibson Bagpipes, Powerlifting, CRM Marketing, New England)
- Adds `makesOffer` block explicitly signaling brand-partnership availability
- Fixes Person.url, Person.brand.url, Person.description, TikTok URL to https
- Preserves the grain overlay (simplified — removed inline SVG for size)

**Script CDN:** https://cdn.prod.website-files.com/6932246fda6d6f12bcacc35f/689e5ba67671442434f3ca35/69e623dc36d1d3e9980b1ffa/rr_head_fix-1.1.0.js

---

## 🔧 Manual action needed in Webflow Designer (MCP can't reach these)

### A. Swap the applied script version 1.0.0 → 1.1.0
**Where:** Webflow Designer → Site Settings → Apps & Integrations → Custom Code / Scripts
**What:** Currently `RR Head Fix v1.0.0` is applied. Change to `v1.1.0` (already registered).
**Why MCP can't:** The `data_scripts_tool` exposes site-level script registration and page-level application, but not site-level application re-assignment.
**Time:** 30 seconds.

### B. Fix the canonical URL at the source (site-level head code)
This is **the highest-leverage single fix on the list.** The broken canonical `<link href="https://www.repsandreels.com" rel="canonical"/>` is server-rendered into the head. My v1.1.0 script fixes it at runtime, but **SEO crawlers and LLM ingesters don't execute JS** — they see the broken server-side version.

**Where:** Webflow Designer → Site Settings → Custom Code → Head Code (top of site)
**What to find and fix:** any hardcoded `<link rel="canonical" href="https://www.repsandreels.com"…` — change to `https://www.repsreels.com/` (or remove entirely; Webflow auto-adds a canonical if you remove the custom one).
**Also in that same head code block:** the JSON-LD `@graph` block. Update all three `"url"` fields from `repsandreels.com` → `repsreels.com`.

### C. Archive/delete the Evolux template pages
Changelog (`/utility-pages/changelog`) and Licenses (`/utility-pages/licenses`) still render on staging. The Webflow API refuses to archive or draft utility pages.
**Where:** Webflow Designer → Pages panel → right-click Changelog / Licenses → Delete.

### D. Enable sitemap.xml
**Where:** Site Settings → SEO tab → "Auto-generate sitemap" → ON.
Currently returns 404. Not accessible via MCP.

### E. Fill in robots.txt
**Where:** Site Settings → SEO tab → robots.txt field. Paste:
```
User-agent: *
Allow: /

User-agent: GPTBot
Allow: /

User-agent: ClaudeBot
Allow: /

User-agent: PerplexityBot
Allow: /

User-agent: Google-Extended
Allow: /

Sitemap: https://www.repsreels.com/sitemap.xml
```

### F. llms.txt
Webflow doesn't serve arbitrary static files at root. Two options:
1. Create a page with slug `llms` (at `/llms`) and paste the content from `seo_audit_2026-04-20.md §6.1`. Not the convention, but findable.
2. Upload as a static asset and add a redirect `/llms.txt` → that asset URL.

Option 1 is fastest. Your call — I can create the page via MCP if you want.

### G. Upload `/og-image.png` to site root
Still a launch-blocker item per launch_checklist.md §1.8. Need a 1200×630 image per image_specs.md §6 Option A. Design task, not an MCP task.

---

## Verification checklist (staging)

Run these to confirm what landed:
```bash
# Title + meta description
curl -s https://reps-reels-320557.webflow.io/ | grep -oE '<title>[^<]+</title>|<meta[^>]*description[^>]*>' | head -3

# 404 rebrand
curl -s https://reps-reels-320557.webflow.io/404 | grep -o '<title>[^<]*</title>'

# Applied script version (should show 1.0.0 applied; 1.1.0 registered but not yet applied — manual swap needed)
```

---

## What's still wrong on staging (needs your backend action)

| Issue | Severity | Fix location |
|---|---|---|
| Canonical still `repsandreels.com` (server-rendered) | 🔴 Critical | Site Settings → Custom Code → Head |
| JSON-LD `url` fields still `repsandreels.com` | 🔴 Critical | Same as above |
| FAQPage schema still being stripped by v1.0.0 script | 🟡 High | Swap applied version to v1.1.0 |
| sitemap.xml 404 | 🔴 Critical | SEO tab → toggle on |
| robots.txt empty | 🟡 High | SEO tab → paste snippet |
| Changelog + Licenses pages still indexable | 🟡 Medium | Pages panel → delete |
| No og-image.png | 🟡 Medium | Design + upload |
| No llms.txt | 🟢 Low-medium | Create /llms page or redirect |

---

## Production (repsreels.com)

**Unchanged.** Still serving the December 2025 publish. Nothing I did touched production.

When you're ready to promote staging → prod:
1. Complete items A-F above in the Designer
2. Re-verify staging
3. Ask me to publish to custom domains: `publish_site` with `customDomains: ["repsreels.com", "www.repsreels.com"]`

Or do it yourself from the Designer's Publish button (uncheck webflow.io, check custom domains).
