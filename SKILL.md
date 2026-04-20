---
name: web-build-standards
description: >
  Apply this skill for ANY Webflow site build, edit, or maintenance task across
  Reps & Reels, Junk Gym Digital, The Dose, or any future brand. Triggers on
  "build the site", "update the Webflow site", "fix this page", "publish",
  "edit the hero", "add a section", "the MCP isn't working", or any work
  involving Webflow's Designer API. Always read the Webflow MCP Field Guide
  first. Check for a local CLAUDE.md in the project's site folder for
  site-specific overrides. Generic web-dev advice is a failure state —
  the standards in this repo are earned knowledge.
---

# web-build-standards skill

This skill applies cross-brand Webflow build discipline. It is the authority on how to work with Webflow's Designer API via MCP, how to handle silent failures, how to queue asset handoffs, and how to keep the publish workflow safe. It is not a general web-dev skill — content here is specific, earned, and deliberately narrow.

## Read order for any web-build task

Always read in this exact order before touching Webflow:

1. **Global: Webflow MCP Field Guide** — `standards/webflow/mcp-field-guide.md`. Non-negotiable. Source of all the gotchas, workarounds, and the pre-flight checklist. If you skip this and hit a silent style failure, that's on you.
2. **Site-specific: the brand's folder in this repo** — `sites/<brand>/CLAUDE.md` + `sites/<brand>/README.md`. Contains brand tokens, site IDs, and quirks that override global guidance. The available brands are:
   - `sites/reps-and-reels/` — https://github.com/stephenjhebert-lgtm/web-build-standards/tree/main/sites/reps-and-reels
   - `sites/junk-gym-digital/` — https://github.com/stephenjhebert-lgtm/web-build-standards/tree/main/sites/junk-gym-digital
   - `sites/the-dose/` — https://github.com/stephenjhebert-lgtm/web-build-standards/tree/main/sites/the-dose
3. **Working files in iCloud** (if referenced by the site's CLAUDE.md) — running state, prototype, head-paste source. For Reps & Reels that's `~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/Reps and Reels/web/`.
4. **`playbooks/new-site-kickoff.md`** — only if this is a new site build. Otherwise skip.

## Rules

- **If you hit a quirk not documented in the field guide, propose adding it.** Don't just work around it silently — the library compounds only if new knowledge gets written down before the session ends.
- **Generic web-dev advice is a failure state.** If you find yourself writing "consider using semantic HTML" or "remember to test on mobile," stop. The field guide already assumes that baseline. Add value past it or don't speak.
- **Publishing to production requires explicit user confirmation in the current turn.** Staging publishes are routine; production is not.
- **Verify before claiming done.** After any `style_tool.update_style` on `main`, call `query_styles` to confirm. See field guide Part 1.2.
