# Contributing

This is a solo repo today, but discipline now saves rework later.

## When to add vs edit

- **Edit an existing standard** if a pattern in the same area gets refined (e.g., a new Webflow MCP quirk → append to `standards/webflow/mcp-field-guide.md`).
- **Add a new standard file** only when an existing file grows past ~50 lines on a single topic and deserves its own home. Create a stub first; split content out later.
- **Site-specific content** (brand tokens, site IDs, quirks particular to one site) belongs in that site's `CLAUDE.md` inside the project folder — **not here**.

## Stub-first policy

Create the stub file before you populate it. A stub is 3–5 lines:
- H1
- One sentence on purpose
- `**Status:** stub — content lives in X today`

This signals intent without pretending completeness.

## Every edit must answer

> What earned knowledge is this?

If the answer is "I read this on MDN" or "this is a general web-dev best practice," it does not belong in this repo. The whole point of this system is that it captures what the docs don't tell you.

## Changelog discipline

Every substantive change bumps `CHANGELOG.md`. Semver-ish:
- `0.x.y` — pre-1.0, anything can change
- Minor version = new standard added, playbook added, or significant restructure
- Patch = refinement, correction, new quirk appended

## PR etiquette

Even solo: open a PR for anything non-trivial, let the GitHub Action run the validation workflow, review your own diff. If the action fails on staleness, decide — update the stale doc or mark it intentionally frozen.
