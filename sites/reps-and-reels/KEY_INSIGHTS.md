# Key Insights — Reps & Reels

Durable lessons from building this site. Not a changelog (see `UPDATE_HISTORY.md`) and not an incident list (see `POSTMORTEMS.md`). These are the things worth remembering on the next project.

## When to add an entry

Add an insight when you learn something that would have changed how you started — a rule of thumb, a constraint, a pattern. If it's only true for this site, still record it; it belongs in CLAUDE.md too.

## Entry template

```
### <Insight title>

**Claim.** One sentence.

**Evidence.** Where it came from (archive path, postmortem date, or live file).

**How to apply.** When to invoke this on future work.
```

---

## Insights

### Visual identity must be briefed before copy

**Claim.** A copy-first brief produces a generic site even when tokens and typography are specified. The crest, hierarchy, and section rhythm need to be in the brief itself.

**Evidence.** 2026-04-20 postmortem — seven rounds of copy- and token-driven iteration did not produce the intended identity; round 8 with a visual-first brief did.

**How to apply.** Any new Webflow build starts with a visual-direction section (crest usage, dark/light rhythm, hero hierarchy) before copy blocks.

---

### Webflow `style_tool` can silently reject valid updates

**Claim.** Certain property combinations at `main` (multi-gradient `background-image`, some `color` / `border-*` / `filter` updates) return success but don't render. Treat this as a known failure mode, not an edge case.

**Evidence.** 2026-04-20 postmortem; five documented workarounds in CLAUDE.md's inline-style table.

**How to apply.** If a class update appears to succeed but QA shows no visual change, skip further class-level attempts — move the declaration into `webflow_head_paste.html` under a helper class.

---

### Custom code → Head is the real source of truth

**Claim.** For this site, `webflow_head_paste.html` is authoritative, not the Webflow Designer style panel. Drift between them caused QA confusion in rounds 2–5.

**Evidence.** `archive/2026-04-19_build/` build logs; current CLAUDE.md section on custom code.

**How to apply.** Never hand-edit styles in the Webflow Designer without syncing `webflow_head_paste.html` in the same session.

---

### Archive everything; do not delete rounds

**Claim.** Keeping every build round, fix prompt, and QA pass as frozen files in `archive/` made the round-8 rebuild much faster — we knew exactly what had been tried.

**Evidence.** `archive/README.md` and the round-by-round folder structure.

**How to apply.** When superseding a draft, move it under `archive/superseded_drafts/` or a dated build folder. Never overwrite in place.
