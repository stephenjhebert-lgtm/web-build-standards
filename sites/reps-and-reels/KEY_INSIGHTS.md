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

---

### Delete Webflow elements rather than fighting their visibility state

**Claim.** If a Webflow element's display state is managed by the Designer's visibility toggle, `add_or_update_attribute` overrides are reverted on every publish. Deleting the element is the only reliable way to make it permanently gone.

**Evidence.** 2026-04-22 postmortem — three rounds of `.reel__placeholder` display overrides all reverted on publish; `remove_element` resolved it in one call.

**How to apply.** On any future Webflow project: if an element is causing layout interference and its display state is Designer-managed, delete it via `remove_element` rather than trying to toggle it. If the element needs to exist as a fallback, set `opacity:0` on the CSS class via `style_tool` instead — that survives publish.

---

### Webflow MCP has two distinct server families; Designer tools require the UUID-prefixed one

**Claim.** The `mcp__webflow__*` prefix covers REST API tools only. Designer-connected tools (`element_tool`, `whtml_builder`, `de_page_tool`, `style_tool`) only work via the UUID-prefixed MCP server (`mcp__8b1796c5-579c-4abd-b629-6d435e81fc97__*`).

**Evidence.** 2026-04-22 postmortem — Designer tools via named prefix returned "No active Designer app connection" consistently; UUID prefix worked immediately.

**How to apply.** At session start, always use the UUID-prefixed variants for any Designer tool. Use `mcp__webflow__*` only for REST API operations (publish, collections, page metadata). If you see "No active Designer app connection," check the prefix before anything else.

---

### Instagram and TikTok standard embeds always include platform chrome

**Claim.** No URL parameter, embed variant, or CSS offset reliably strips the profile header and action bar from standard IG/TikTok iframes. The chrome is baked into the embed system.

**Evidence.** 2026-04-22 postmortem — multiple cropping approaches failed or degraded embed functionality. Accepted chrome-visible embeds for launch.

**How to apply.** If a client requires chrome-free social video display, spec a third-party aggregator tool (Behold.so for Instagram, similar for TikTok) from the start — do not plan to strip chrome via CSS. This is a platform constraint, not a solvable engineering problem with standard embeds.

---

### Verify Webflow plan CMS access before speccing CMS-dependent features

**Claim.** Webflow Basic plan does not include CMS Collections. Building a CMS-powered feature on Basic will fail at collection creation with a 409.

**Evidence.** 2026-04-22 postmortem — Reels CMS Collection creation returned 409 "Upgrade to CMS Hosting."

**How to apply.** At project kickoff, call `sites_get` and confirm the plan includes CMS before writing any spec that uses Collections. If plan is Basic, design for static hardcoded content from the start (Option B pattern from `reels_embed_spec.md`).

---

### Avoid `calc()` in `whtml_builder` inline styles

**Claim.** CSS `calc()` expressions in inline styles passed through `whtml_builder` are unreliable — they may be stripped or cause unexpected element behavior on publish.

**Evidence.** 2026-04-22 postmortem — `height:calc(100% + 220px)` on iframes caused TikTok embeds to degrade to static preview cards.

**How to apply.** For any sizing that requires `calc()`, add the rule to `webflow_head_paste.html` as a class-level CSS rule instead of using inline styles. Use simple absolute values or percentage-only values in `whtml_builder` inline styles.
