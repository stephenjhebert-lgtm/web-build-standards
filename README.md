# web-build-standards

Cross-brand web build standards for Webflow sites. Global rules live here; site-specific overrides live in each site's project folder as a local `CLAUDE.md`.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  GLOBAL LAYER — this repo (github.com/stephenjhebert-lgtm)   │
│  standards/ • templates/ • playbooks/ • SKILL.md             │
│  Fetched by Claude at build time via raw.githubusercontent   │
└─────────────────────────────────────────────────────────────┘
                              ▲
                              │  read first, always
                              │
┌─────────────────────────────────────────────────────────────┐
│  SITE-SPECIFIC LAYER — in each project folder, local only    │
│  CLAUDE.md  (overrides + site context)                       │
│  source-of-truth.md  (running site state)                    │
│  webflow_head_paste.html  (synced custom code block)         │
└─────────────────────────────────────────────────────────────┘
```

Claude fetches files from this repo via:

```
https://raw.githubusercontent.com/stephenjhebert-lgtm/web-build-standards/main/<path>
```

Entry point for the skill: [`SKILL.md`](./SKILL.md).

## Brand registry

Each brand has its own folder under [`sites/`](./sites) containing a `README.md` (registry entry) and `CLAUDE.md` (site-specific Claude context).

| Brand | Folder | Status |
|---|---|---|
| Reps & Reels | [`sites/reps-and-reels/`](./sites/reps-and-reels) | Shipped to staging 2026-04-20 |
| Junk Gym Digital | [`sites/junk-gym-digital/`](./sites/junk-gym-digital) | Not yet built |
| The Dose | [`sites/the-dose/`](./sites/the-dose) | Not yet built |

## How to use

- **Any web task** — read order is always: (1) global [field guide](./standards/webflow/mcp-field-guide.md) → (2) the brand's `sites/<brand>/CLAUDE.md` → (3) working files referenced by that CLAUDE.md. See [`SKILL.md`](./SKILL.md).
- **Starting a new site?** Run the checklist in [`playbooks/new-site-kickoff.md`](./playbooks/new-site-kickoff.md) and populate `sites/<brand>/CLAUDE.md` from [`templates/site-claude-md-template.md`](./templates/site-claude-md-template.md).
- **Hit a new Webflow quirk?** Add it to [`standards/webflow/mcp-field-guide.md`](./standards/webflow/mcp-field-guide.md) before moving on. That's how the pattern library compounds.
- **Need to update a standard?** Edit the file, bump [`CHANGELOG.md`](./CHANGELOG.md). See [`CONTRIBUTING.md`](./CONTRIBUTING.md).

## License

MIT. See [`LICENSE`](./LICENSE).
