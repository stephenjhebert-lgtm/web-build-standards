# CLAUDE.md — Junk Gym Digital

**Status:** Active build — staging live, production not yet connected
**Last updated:** 2026-04-22

---

## Site identity

- **Brand:** Junk Gym Digital
- **Positioning:** CRM and retention marketing consultancy. 
- **Domain:** junkgym.com
- **Webflow site ID:** 68ad03867bd00372bd73e906
- **Staging URL:** https://junk-gym-email-marketing.webflow.io
- **Custom domain:** junkgym.com (DNS not yet connected as of 2026-04-22)

## Brand tokens (CSS custom properties)

```css
:root {
  --ink: #0B0F14;
  --paper: #F6F7F9;
  --rust: #D94F28;
  --rust-hover: #B8401F;
  --line: #1E242D;
  --text-primary-dark: #FFFFFF;
  --text-secondary-dark: #B7C0CC;
  --text-primary-light: #0B0F14;
  --text-secondary-light: #4A5260;
  --text-muted: #8892A0;
  --signal-green: #3CB371;
  --signal-warn: #E6A23C;
  --muted-gold: #C9A961;
}
```

## Fonts
- Inter Tight (600, 700) — headings
- Inter (400, 500, 600) — body
- JetBrains Mono (500) — mono labels/eyebrows

## Element IDs (top-level homepage sections)

| Section | Element ID |
|---|---|
| Nav | ae6e4411-832c-4aae-24c4-a3f7dabf2ad2 |
| §01 Hero | d2c1f6c0-431e-dc89-ee90-506a8a0ca832 |
| §02 Two-path | 510024ac-879c-118f-691d-0311b1fa2d8d |
| §03 Proof strip | e57242b4-3e14-4793-4c43-3dcc7fefd5d4 |
| §04 How it works | cd3b7c4b-b89f-1f3a-7ed9-d42d1a7497a1 |
| §05 About | 554800f3-0d1b-774d-0506-b068b62a8527 |
| §06 Final CTA | 01c72a19-eb02-60a9-c2b5-e96a2019e138 |
| Footer | 5d9e3fff-2ff5-5473-b848-03149ce509e3 |

## Pages

| Page | Slug | Page ID | Status |
|---|---|---|---|
| Homepage | / | 68ad03887bd00372bd73e94a | Live on staging |
| Contact | /contact | [to fill when created] | NOT YET CREATED |
| 404 | /404 | — | Default Webflow 404 (not customized) |

## Site scripts (registered inline, auto-applied)

Scripts were cleaned up 2026-04-22. All live scripts are v2.0.0. Legacy v1.0.0 scripts remain registered but are not applied — cosmetic cleanup only, no site impact.

| Script ID | Location | Version | Status |
|---|---|---|---|
| jgd_base_css_2 | header | 2.0.0 | Live |
| jgd_tracking_2 | footer | 2.0.0 | Live |
| jgd_meta_complete_2 | header | 2.0.0 | Live |
| jgd_person_schema_2 | header | 2.0.0 | Live |
| jgd_services_schema_2 | header | 2.0.0 | Live |
| jgdfaqschema_2 | header | 2.0.0 | Live |
| jgd_schema_jsonld | — | 1.0.0 | Registered only, NOT applied — delete from registry when convenient |

## Open HITL queue (Steve must complete before launch)

- [ ] Create `/contact` page in Webflow Designer (MCP can't create pages — Designer app must be open)
- [ ] Build Tally form → get form ID → replace `{{TALLY_FORM_ID}}` in contact embed
- [ ] Provision GA4 property → replace `{{GA4_MEASUREMENT_ID}}` in jgd_ga4 script
- [ ] Add site to Google Search Console → replace `{{GSC_VERIFICATION_STRING}}`
- [ ] Upload OG image (1200×630 PNG) to Webflow Asset Manager → update og:image references
- [ ] Upload favicon set + Apple touch icon → Project Settings → Favicon
- [ ] Upload Steve's headshot → wire into §05 About section (replaces placeholder tile)
- [ ] Paste robots.txt in Project Settings → SEO (text in HANDOFF-2026-04-20.md)
- [ ] Toggle sitemap auto-generation ON in Project Settings → SEO
- [ ] Connect junkgym.com custom domain in Project Settings → Hosting
- [ ] Explicit go-ahead publish to production
- [x] ~~Delete `jgd_schema_jsonld` script~~ — Done 2026-04-22 (replaced by jgd_person_schema_2 + jgd_services_schema_2)

## Known inline-style workarounds

| Element | Property | Reason | Date |
|---|---|---|---|
| (none applied yet — `jgd_base_css` script handles token injection) | — | — | — |

## Build history

- 2026-04-19: Full homepage build (8 sections) via Claude Code + Webflow MCP
- 2026-04-20: SEO + AI discoverability audit; 5 additional scripts registered
- 2026-04-22: Pre-launch session — script cleanup, contact page attempt, integration audit, QA pass, launch readiness report
