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

| Brand | Project folder | Status |
|---|---|---|
| Reps & Reels | `~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/Reps and Reels` | In build |
| Junk Gym Digital | `~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/JunkGymDigital` (planned) | Not yet built |
| The Dose | `~/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/TheDose` (planned) | Not yet built |

Full per-site entries live in [`sites/`](./sites).

## How to use

- **Starting a new site?** Run the checklist in [`playbooks/new-site-kickoff.md`](./playbooks/new-site-kickoff.md).
- **Working on an existing site?** The site's `CLAUDE.md` lives in the project folder, not here. Read the global [field guide](./standards/webflow/mcp-field-guide.md) and the local `CLAUDE.md`, in that order.
- **Hit a new Webflow quirk?** Add it to [`standards/webflow/mcp-field-guide.md`](./standards/webflow/mcp-field-guide.md) before moving on. That's how the pattern library compounds.
- **Need to update a standard?** Edit the file, bump [`CHANGELOG.md`](./CHANGELOG.md). See [`CONTRIBUTING.md`](./CONTRIBUTING.md).

## License

MIT. See [`LICENSE`](./LICENSE).
