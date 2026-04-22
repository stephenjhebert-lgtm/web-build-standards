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

**Fix / mitigation.** Every known case is logged in `CLAUDE.md` and `SOURCE_OF_TRUTH.md`. Canonical workaround is to carry the styling in `webflow_head_paste.html` under a helper class or `aria-hidden` shim.

**Prevention.** Read CLAUDE.md's inline-style table before touching any of those elements. If a class-level update appears to succeed but doesn't render, assume silent reject and use the head-paste path.

---

### 2026-04-22 — Webflow Basic plan blocks CMS Collections

**What happened.** Attempted to create a `Reels` CMS Collection to power the 6 social embed cards dynamically. The API returned 409 Conflict: "Upgrade to CMS Hosting."

**Why.** Webflow Basic plan does not include CMS. The build spec assumed CMS would be available (see `reels_embed_spec.md` Option A). It isn't on this account's current plan.

**What it cost.** One approach abandoned mid-implementation. Pivoted to Option B (6 static hardcoded iframes via `whtml_builder`).

**Fix / mitigation.** All 6 reels are hardcoded as `position:absolute` iframes inside each `.reel` card. Post/platform mapping documented in `SOURCE_OF_TRUTH.md`.

**Prevention.** Check Webflow plan tier before speccing any CMS-dependent feature. `sites_get` returns the plan — verify CMS is enabled before writing a CMS-dependent spec.

---

### 2026-04-22 — Webflow Designer MCP requires UUID-prefixed tools, not `mcp__webflow__*`

**What happened.** Designer tools (`de_page_tool`, `element_tool`, `whtml_builder`) called via the `mcp__webflow__` prefix returned "No active Designer app connection" even with the Designer open and connected.

**Why.** Two separate MCP server registrations exist: `mcp__webflow__*` (REST API only, no Designer connection) and the UUID-prefixed server `mcp__8b1796c5-579c-4abd-b629-6d435e81fc97__*` (Designer-app-connected). They are not interchangeable.

**What it cost.** One reconnect cycle; Designer tools failed until the correct prefix was identified.

**Fix / mitigation.** All Designer tool calls used the UUID prefix. REST API calls (publish, page metadata) used `mcp__webflow__*`.

**Prevention.** If any `de_*`, `element_tool`, `whtml_builder`, or `style_tool` call returns "No active Designer app connection," switch to the UUID-prefixed MCP variant immediately. The two prefixes serve different tool families.

---

### 2026-04-22 — Webflow `display:none` inline style cannot be overridden via `add_or_update_attribute`

**What happened.** The 6 `.reel__placeholder` elements had Webflow-managed inline `style="display:none;"`. Setting `style="display:block;"` via `add_or_update_attribute` appeared to succeed but was reverted to `display:none` on every publish.

**Why.** Webflow serializes the Designer's element visibility toggle as an inline `style` attribute and re-applies it on publish. The MCP attribute write is overwritten by Webflow's authoritative hidden state at publish time.

**What it cost.** Three fix rounds: attribute override (reverted), CSS `!important` approach (uncertain), iframe offset/crop (broke embed display). Resolution required deleting the placeholder elements entirely.

**Fix / mitigation.** Deleted all 6 `.reel__placeholder` elements via `remove_element`. Nothing to revert; no overlay.

**Prevention.** Do not try to toggle Webflow Designer visibility state via `add_or_update_attribute`. Reliable options only: (a) delete the element, (b) set `opacity:0` on the CSS class via `style_tool`, (c) unhide in the Designer UI itself. Never rely on inline `style` attribute overrides for display state on Webflow-managed elements.

---

### 2026-04-22 — `calc()` in `whtml_builder` iframe inline styles broke embed display

**What happened.** To crop Instagram/TikTok platform chrome, iframes were inserted with `style="position:absolute;top:-62px;height:calc(100% + 220px);"`. On publish, TikTok embeds degraded from full video player to static preview cards.

**Why.** Two compounding issues: (1) Webflow may not preserve `calc()` expressions in inline styles when serialized through `whtml_builder`; (2) the `-62px` top offset moved the iframe's interactive trigger zone above the visible area, preventing TikTok's player from initializing.

**What it cost.** Two additional insert/delete cycles; one extra publish; TikTok showed as non-interactive preview cards.

**Fix / mitigation.** Reverted to simple `inset:0; width:100%; height:100%` on all iframes. Platform chrome is visible but embeds display correctly.

**Prevention.** Avoid `calc()` in `whtml_builder` inline styles — results are unreliable. For iframe cropping, add a CSS class rule to `webflow_head_paste.html` instead of using inline styles. Note: Instagram and TikTok standard embeds **always** include platform chrome (profile header, action bar). No URL parameter removes it. If chrome-free display is required, use a third-party tool (e.g., Behold.so for Instagram) rather than fighting the embed API.

---

### 2026-04-22 — `add_inline_site_script` returns 403 for this account

**What happened.** Attempted to inject CSS via `add_inline_site_script`. The call returned HTTP 403 "token not authorized for this API version."

**Why.** The Webflow API token for this MCP does not have the `custom_code:write` scope, or the plan restricts the Custom Code API endpoint.

**What it cost.** One approach abandoned; pivoted to `whtml_builder` CSS parameter and `style_tool` for style injection.

**Fix / mitigation.** All custom CSS lives in `webflow_head_paste.html` (pasted into Webflow Site Settings → Custom Code → Head).

**Prevention.** Do not plan around `add_inline_site_script` for this site. Use `webflow_head_paste.html` as the canonical CSS source.
