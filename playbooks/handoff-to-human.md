# Handoff-to-human playbook

When to stop and ask Steve to do something in Webflow directly. Drawn from field guide Part 10.

## Confirmed MCP gaps (always require human)

- **Asset upload.** `asset_tool` has no upload endpoint. Every new image/font/video must be dragged into the Webflow Assets panel by a human.
  - Message to Steve: _"Drop this file into Webflow Assets: `<path>`. Tell me when it's in and I'll wire it up."_
- **Plan / billing changes.** Upgrades, domain purchases, workspace changes.
- **Some site settings.** SEO defaults in certain cases, redirects UI, privacy/cookie banner config.

## Judgment calls (escalate, don't autopilot)

- **Publishing to production** (custom domain). Requires explicit "publish to production" from Steve in the current turn. Staging (`.webflow.io`) is routine.
- **Structural rewrites of existing sections** that would discard Steve's in-Designer work.
- **Deleting CMS collections or pages.** Irreversible.
- **Custom code beyond the standard Head/Body blocks** — e.g., per-page embeds with auth requirements.

## How to hand off cleanly

1. State exactly what you need and where.
2. State what you'll do once it's done.
3. Pause. Don't proceed on assumption.

Example:

> I need the new hero image in Webflow Assets before I can wire it up.
> File to drag in: `/Users/steve/Downloads/hero-v3.png` → folder `2026/homepage`.
> Once it's in, say "done" and I'll:
> 1. Re-list assets, capture the new asset ID.
> 2. Set it on the hero element.
> 3. Publish to staging and verify.
