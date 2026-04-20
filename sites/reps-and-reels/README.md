# Reps & Reels

- **Webflow site ID:** `6932246fda6d6f12bcacc35f`
- **Home component ID:** `69322470da6d6f12bcacc3bb`
- **Staging URL:** `repsreels.webflow.io`
- **Custom domain:** `repsreels.com` (not yet connected)
- **Status:** Homepage shipped to staging 2026-04-20; custom domain + live embeds deferred
- **Last updated:** 2026-04-20

## Layout

All site context lives in this folder. The local iCloud folder holds only pointer stubs and `.claude/launch.json`.

### Authoritative state
- `CLAUDE.md` — site-specific Claude instructions and overrides (read after global standards)
- `SOURCE_OF_TRUTH.md` — page structure, assets, inline-style log, version history
- `webflow_head_paste.html` — canonical custom CSS pasted into Webflow Site Head

### Learning and history
- `POSTMORTEMS.md` — incidents, root causes, preventions
- `KEY_INSIGHTS.md` — durable lessons
- `UPDATE_HISTORY.md` — higher-altitude changelog

### Specs and content
- `homepage_copy.md` — homepage copy deck
- `homepage_prototype.html` — design reference
- `image_specs.md` — image sizing and naming
- `seo_package.md` — SEO metadata, OG tags, structured data
- `reels_embed_spec.md` — reel card spec
- `IG_TT_INTEGRATION.md` — IG / TikTok embed integration plan
- `launch_checklist.md` — go-live checklist
- `fpo/` — FPO (for-position-only) reel thumbnails

### History
- `archive/` — frozen per-round build logs, fix prompts, QA passes, superseded drafts. Read-only.

## Build notes
- Field guide was originally authored during this project's first build.
- Seven build rounds (2026-04-19) were superseded by a crest-forward round-8 rebuild on 2026-04-20. See `POSTMORTEMS.md`.
