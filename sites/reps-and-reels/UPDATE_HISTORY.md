# Update History — Reps & Reels

Chronological log of meaningful changes to the site, this repo folder, or the working setup. One line per change. Newest first.

Fine-grained page structure and asset state lives in `SOURCE_OF_TRUTH.md`. This file is the higher-altitude changelog — what shifted and when.

## Format

```
## YYYY-MM-DD — <short title>
- Bullet per change. Link archive paths or commits where useful.
```

---

## 2026-04-20 — Project context moved to GitHub

- Site docs, build archive, FPO assets, and custom-code source relocated from iCloud into `sites/reps-and-reels/` on `web-build-standards`.
- Local iCloud folder now holds pointer stubs only (except `.claude/launch.json` and `CLAUDE.md` pointer).
- Added `POSTMORTEMS.md`, `KEY_INSIGHTS.md`, and this file.
- CLAUDE.md and README.md updated to reflect the new layout.

## 2026-04-20 — Round 8 rebuild shipped to staging

- Crest-forward homepage shipped at `repsreels.webflow.io` after seven superseded rounds.
- Five inline-style workarounds logged in CLAUDE.md due to `style_tool` silent rejections.
- SEO package + OG tags applied (see `seo_package.md`).
- `SOURCE_OF_TRUTH.md` locked at 162 lines as the authoritative state.
- Deferred: custom domain, live IG/TT embeds, Omnisend form, sticker/tattoo crest variant.

## 2026-04-19 — Initial build rounds 1–7

- See `archive/2026-04-19_build/` for per-round build logs, fix prompts, and QA passes.
- Superseded by round 8 after 2026-04-20 audit.
