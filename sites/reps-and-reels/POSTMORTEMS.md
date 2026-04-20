# Postmortems — Reps & Reels

Short, blame-free writeups for anything that went meaningfully wrong or produced surprising rework. One entry per incident. Keep them terse.

## When to add an entry

- A build round was discarded or had to be redone.
- A Webflow behavior caused a workaround that now lives in `SOURCE_OF_TRUTH.md` or `CLAUDE.md`.
- A deploy broke the staging or live site.
- A decision was reversed after shipping.

## Entry template

```
### YYYY-MM-DD — <short title>

**What happened.** 1–3 sentences.

**Why.** Root cause, not symptom.

**What it cost.** Rounds redone, hours lost, content re-shot, etc.

**Fix / mitigation.** What was done. Link the commit, the archive folder, or the SOURCE_OF_TRUTH row.

**Prevention.** The rule or check we added so this doesn't repeat. If nothing changed, say so and why.
```

---

## Entries

### 2026-04-20 — First build was too dark, too small, too bland

**What happened.** The initial build (rounds 1–7, see `archive/2026-04-19_build/`) shipped a homepage that the morning audit rejected: hero too dim, type too small, crest under-used, overall "generic dark fitness site" rather than the intended brass-forward identity.

**Why.** The initial brief leaned on tokens and copy but under-specified visual hierarchy and the role of the crest. Rounds 2–7 fixed local issues (spacing, a11y, QA) without re-examining the top-level direction.

**What it cost.** Seven build rounds archived; round 8 was a clean rebuild against a new redesign prompt.

**Fix / mitigation.** `archive/2026-04-20_audit/claude_code_round8_redesign_prompt.md` re-briefed the build with crest-foregrounded direction and alternating dark/bone-paper rhythm. Result is the current `SOURCE_OF_TRUTH.md`.

**Prevention.** CLAUDE.md now lists brand tokens and the crest role explicitly so future rebuilds start from visual identity, not just copy.

---

### 2026-04-20 — Webflow `style_tool` silently rejects class-level updates

**What happened.** Several class-level style updates (marquee `background-color`, stats-band `color`, voice-cornerstone `border-*`, nav/footer mark `filter` + sizing, hero multi-gradient `background-image`) returned success from `style_tool` but did not render.

**Why.** Webflow's MCP `style_tool` accepts the call but drops certain property combinations at `main` without error. Cause unknown; reproducible on the listed properties.

**What it cost.** Multiple QA rounds chasing "why isn't this applying"; several helper divs and injected head CSS rules added as workarounds.

**Fix / mitigation.** Every known case is logged in `CLAUDE.md` ("Known inline-style workarounds") and `SOURCE_OF_TRUTH.md`. Canonical workaround is to carry the styling in `webflow_head_paste.html` under a helper class or `aria-hidden` shim.

**Prevention.** Read CLAUDE.md's inline-style table before touching any of those elements. If a class-level update appears to succeed but doesn't render, assume silent reject and use the head-paste path.
