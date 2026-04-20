<!-- Promoted from Reps & Reels project folder on 2026-04-20. Source of truth is now this file. -->
# Webflow MCP — Field Guide

**Purpose:** Practical lessons for building or maintaining any Webflow site through Claude + the Webflow MCP. Distilled from real blockers hit during site builds, not from documentation. Applies to any site, not a specific one.

**Audience:** Anyone (Steve, Claude, or a handoff dev) doing Webflow work through MCP. Not a substitute for the Webflow docs — a supplement for the potholes the docs don't mention.

---

## TL;DR — the 5 time-savers

1. **Never trust a `style_tool` success response.** The tool can report "Style updated" and the property silently doesn't persist. Always verify with `query_styles` after any update on the `main` breakpoint or when using complex values.
2. **The `main` breakpoint rejects certain property values silently.** Multi-layer gradients, stacked `drop-shadow` filters, some border shorthands, and occasionally plain `color` / `background-color` updates on existing classes just don't land. Fallback order: inline style → injected helper div with CSS → split into simpler properties.
3. **Webflow MCP has no asset upload endpoint.** `asset_tool` lists, folders, and renames — that's it. New assets require a human to drag files into Webflow's Assets panel. Plan for this handoff.
4. **Webflow is desktop-first, despite what your design brief says.** `main` = desktop, then `medium` (≤991), `small` (≤767), `tiny` (≤479). Properties cascade down unless overridden. Mobile-first thinking will bite you.
5. **Inline `style=""` attributes always win** and are the escape hatch when class updates are being rejected. The tradeoff: inline can't be responsive — one value across all breakpoints.

---

## Part 1 — Webflow MCP bugs and silent failures

### 1.1 The "silent success" pattern

**Symptom:** `style_tool.update_style` returns a success response. `query_styles` immediately after shows the update didn't persist — the style still has the old value.

**When it hits:**
- Updating `background-image` with multiple stacked gradients on `main`
- Updating `filter` with multiple stacked `drop-shadow()` values on `main`
- Updating `background-color` on some existing classes at `main`
- Updating `color` on some existing classes at `main`
- Updating `border-left` / `border-right` / border shorthand on some classes
- Combining a complex property (filter, gradient) with simple sizes in the same update call — the whole thing gets rejected

**Why (best guess):** Webflow's Designer API appears to validate property values against an internal whitelist on the main breakpoint that doesn't accept all values the CSS spec allows. Non-main breakpoints (`medium`, `small`, `tiny`) are more permissive — same values often go through there fine.

**What this means operationally:** every class-level style update on `main` with anything non-trivial needs a verification read immediately after.

### 1.2 Verification pattern

After any `update_style` call on `main`:

```
style_tool.query_styles({
  queries: [{
    name_path: ["your-class"],
    include_properties: true,
    include_all_breakpoints: true
  }]
})
```

Check the returned properties object against what you just tried to write. If they don't match, apply a workaround (below) — don't retry the same call and hope.

### 1.3 Other deferred-success behaviors

- **Removing a property via `remove_properties`** sometimes reports success but leaves the property in place. Same verification rule.
- **Setting a property back to its default value** (e.g., `position: static`) sometimes behaves as "no-op" and doesn't clear a previously set value.
- **Updating multiple properties at once** occasionally fails silently on one property while succeeding on the others. Verify every property individually on anything important.

---

## Part 2 — Workaround patterns (in order of preference)

### 2.1 Simplify the value

First, try splitting a compound property into simpler atomic updates:
- Instead of `filter: drop-shadow(...) drop-shadow(...)`, try updating with one shadow first, verify, then add the second.
- Instead of `background-image: radial-gradient(...), radial-gradient(...), linear-gradient(...)`, try a single radial + solid background-color.

Often enough of the value lands to ship without a workaround.

### 2.2 Inline `style=""` attribute

When the class update keeps failing, fall back to `element_tool.add_or_update_attribute` with the `style` attribute:

```
element_tool.add_or_update_attribute({
  id: { component: "...", element: "..." },
  attributes: [{ name: "style", value: "filter: drop-shadow(...) drop-shadow(...); height: 44px;" }]
})
```

**Advantages:** always wins. CSS specificity is high; no dependency on Webflow's validator.
**Disadvantages:** not responsive (can't include `@media`). Overrides the class at every breakpoint. Harder to maintain — the "source of truth" is split between class rules and inline.

Always document inline-style uses in the source-of-truth file so they're not lost.

### 2.3 Helper DOM element with injected CSS

When you need responsive behavior that the class can't hold, inject a small DOM element via `whtml_builder` with a `<style>` block inside. The `<style>` tag can contain `@media` rules, complex gradient stacks, anything.

Example pattern: add an empty `<div class="section__atmosphere" aria-hidden="true">` inside the section you want to style, and have the injected `<style>` carry the full gradient stack at multiple breakpoints. Webflow treats it as injected HTML and doesn't try to validate the CSS.

This is also the pattern for forcing a style on a class that can't be updated via `style_tool`: inject a tiny helper div plus a `<style>` block with `.target-class { color: #0b0b0c; }` in it. The helper div itself can be `display: none`.

**Note on Webflow's body-end injection:** `whtml_builder` can only add HTML where it's called. If you need site-wide CSS, putting it in Webflow Site Settings → Custom Code → Head is cleaner than injecting on every page.

### 2.4 Custom code in Site Head

The sanest long-term home for complex CSS. Webflow's Custom Code → Head block accepts raw CSS in `<style>` tags without validation. Use this for:
- `@font-face` declarations
- `:root` custom property definitions
- Component styles that need `@media` queries
- Keyframe animations
- Anything Webflow's style_tool is rejecting that you don't want inline

**Discipline:** keep the paste-in block in a local `webflow_head_paste.html` file. Edit there, paste into Webflow. Never hand-edit in Webflow without syncing back, or the two will drift and you'll lose track of truth.

---

## Part 3 — Asset management

### 3.1 No upload endpoint

Webflow MCP's `asset_tool` exposes:
- `get_all_assets_and_folders` — list everything
- `create_folder` — yes
- `update_asset` — rename, move, set alt text

That's it. There is no `upload_asset` or equivalent. New assets must be added by a human via the Webflow Designer.

**Workflow:**
1. Hand the file path to the human: "Drop this into Webflow Assets panel: `/path/to/file.png`"
2. Wait for confirmation
3. Re-list assets and find the new one by filename
4. Capture the asset ID for reference
5. Use `element_tool.set_image_asset` to wire it in

**Don't try to route around this** via Chrome automation or direct HTTP uploads — the MCP doesn't provide an API token, and browser automation on the Webflow Designer is fragile.

### 3.2 Asset hygiene

- Set `altText` on the asset record itself via `update_asset`, not just on the element. Webflow uses the asset-level alt as the default.
- Rename uploads to match a convention before using them. `IMG_0042.png` in your Webflow Assets library is technical debt.
- When retiring an asset (e.g., swapping in a new logo), don't delete it — just stop referencing it. Deletion can break cached pages. Document the retirement in the source-of-truth file.

### 3.3 Image aspect ratios

Don't set `width: Xpx; height: Xpx` on an image whose natural aspect isn't square. It'll squish — `object-fit: contain` helps but still wastes space.

Use `height: Xpx; width: auto; max-width: Ypx; object-fit: contain` to scale by height with an aspect-preserving width. Or lock by width with `height: auto`.

---

## Part 4 — The breakpoint cascade (desktop-first reality)

### 4.1 How Webflow breakpoints inherit

Webflow uses desktop-first CSS. The `main` breakpoint is the base; smaller ones inherit from it and override selectively.

```
main (desktop, 992px+)
  ↓ inherits
medium (≤991px)
  ↓ inherits
small (≤767px)
  ↓ inherits
tiny (≤479px)
```

**Consequence:** if you set `flex-direction: row` on `main`, you get `row` everywhere unless you explicitly override on a smaller breakpoint. A "mobile-first" brief translates in Webflow to "set everything you want on mobile at main, then override on main for desktop if different" — which is backwards from what the brief usually says.

### 4.2 The trap

When Claude or a dev thinks "I'll set this on mobile first," they'll set it on `small`. That works for mobile but leaves the `main` breakpoint untouched, inheriting Webflow's defaults — which may not be what the design intended. Always ask: what does this look like on desktop? If you haven't explicitly set `main`, Webflow's default is probably wrong.

### 4.3 Pattern for mobile-priority designs

If the design priority is genuinely mobile:
1. Set the ideal mobile styles on `main` first (yes, it's weird)
2. Override on `main` only the properties that need to differ on desktop
3. Add small/tiny adjustments only for device-specific tweaks

This looks like "desktop styles modify mobile styles," which is inverted from typical thinking but matches Webflow's model.

### 4.4 Breakpoint-specific property rejection

Observation: `main` rejects some complex values (see Part 1). The same value applied to `medium` or `small` often goes through without complaint. If a value keeps failing on `main`, try applying it to `medium` (which covers 767+) and leave `main` bare — it'll inherit down. Imperfect but sometimes the fastest path.

---

## Part 5 — Element editing discipline

### 5.1 Accent color spans

Webflow's text elements don't support inline coloring directly — you need to break a String child into multiple children with spans wrapping the accent words. This is structural, not text-editing.

**Pattern to color one word inside a heading:**
1. Fetch the current heading's children tree
2. Remove the existing String child
3. Add back: String("prefix ") + Span with child String("accent word") + String(" suffix")
4. Create a style for the span with the accent color
5. Apply that style class to the span

Every time someone asks to "make one word brass" in a Webflow heading, this is the actual work.

### 5.2 Text replacement gotchas

- `element_tool.set_text` works on an element that has a single String child. If the element has a mixed tree (spans + strings), `set_text` may collapse the structure. Check first.
- Copy with smart quotes (`’`) will render as-is. Decide if you want them or straight quotes consistently.
- Non-breaking spaces (`&nbsp;`) set via MCP sometimes revert to regular spaces. For required non-breaking spaces (between numbers and units, etc.), use CSS `white-space: nowrap` on a wrapper span instead.

### 5.3 Attribute edits are cheap; structural edits are not

Changing an `alt` attribute, adding inline `style`, updating an `aria-label` — all reversible and easy.

Changing an element's type, re-parenting, inserting a new child in the middle of existing children — all structural and can break layout. Budget more time than you think.

---

## Part 6 — Session hygiene

### 6.1 Tool loading

When working on a large site, the MCP exposes many deferred tools. Loading them one by one is slow:

- `ToolSearch` with `select:tool1,tool2,tool3,...` loads named tools in one call
- `ToolSearch` with a keyword like `webflow` loads matching tools, up to `max_results`
- For the full Webflow toolkit, a broad query returns everything in one shot

### 6.2 Local state files

Long sessions can hit context compaction. When compaction happens, Claude loses the working set but keeps the summary. Any state that isn't written to a file is lost.

**Discipline:** as you build, keep a running file like `current_state.md` (or update the source-of-truth doc in place) with:
- Site ID and component IDs (easy to forget, hard to rediscover)
- Element IDs of anything you've edited
- Asset IDs of anything you've referenced
- Style class names you've created or modified
- Inline-style workarounds applied (so they're not lost in future refactors)

This turns "resume after compaction" from "read 50KB of summary" into "open one file and have full state."

### 6.3 Verify before you claim done

Especially for a user-facing "ready to ship" moment: after the final publish, fetch the element tree once more and confirm the pieces you edited actually reflect in the live structure. Webflow occasionally caches a publish or silently drops an edit. A 30-second verification read is cheaper than a "why is the logo still the old one?" bug report the next morning.

---

## Part 7 — Safe publish workflow

### 7.1 Staging vs custom domain

`data_sites_tool.publish_site` with `publishToWebflowSubdomain: true` publishes to `your-site-id.webflow.io` (or the configured subdomain). This is safe — it's the staging URL.

Publishing to `customDomains` pushes to the live production URL (e.g., `yourbrand.com`). This should always require explicit user confirmation. Treat it like a production deploy: don't do it on autopilot, don't do it as part of a routine "save" flow, don't do it without the user saying "publish to production" in the current turn.

### 7.2 Pre-publish quick check

Before any publish, especially production:
- Run `query_elements` on the home (or changed) page to confirm structure matches expectations
- Run `query_styles` on any classes you've edited to confirm values persisted
- Verify no placeholder text ("Lorem...", "[ placeholder ]") is in a production-visible section unless it's deliberately a stub
- Check that links to `#` or `javascript:void(0)` haven't been committed

### 7.3 Post-publish verification

After publishing, hit the staging URL in a fresh browser tab (incognito) to bypass Webflow's own caching. Scroll through. If something looks off, compare against the prototype/reference.

---

## Part 8 — Pre-flight checklist for any Webflow build

Before starting work on a new site or a significant change to an existing one:

- [ ] **Capture the site ID and any component IDs up front** in a local file. You'll need them constantly.
- [ ] **Confirm which breakpoints the design targets** (desktop-only, responsive, mobile-priority). Plan the `main` → `medium` → `small` → `tiny` cascade before touching styles.
- [ ] **List the assets that need to exist.** If any are not already in Webflow Assets, queue the human drag-in step at the start, not in the middle of a flow.
- [ ] **Keep a `webflow_head_paste.html`** (or equivalent) locally for custom CSS/JS. Edit there, paste into Webflow. Never hand-edit Webflow custom code without updating the local file.
- [ ] **Plan the verification strategy.** For any style update on `main`, plan to query-and-confirm after. Don't trust success responses.
- [ ] **Have a source-of-truth document** for the site from day one. It's easier to keep up to date than to reconstruct after the fact.
- [ ] **Decide the publish cadence.** Publish to staging often (low cost, easy to verify). Publish to production only on explicit go-ahead.
- [ ] **Document workarounds as you apply them.** Every inline-style override, every injected helper div, every "this had to go in Site Head because style_tool rejected it" — note the reason. Future-you will thank current-you.

---

## Part 9 — Anti-patterns (things that look smart but aren't)

- **"Let me just retry the style update, maybe it was a fluke."** It wasn't. If the verification shows the update didn't persist, the retry will also not persist. Go to workaround.
- **"I'll update everything at once in a batch of 10 property changes."** If one silently fails, you may not notice until the site renders wrong. Batch similar changes, but verify each logical group.
- **"I'll just hand-edit this one thing in Webflow real quick."** Now the local `webflow_head_paste.html` and Webflow's actual head are out of sync. Either always do it through MCP, or always do it in Webflow and sync back. Mixing is how truth drifts.
- **"The MCP will handle the asset upload — let me just try."** It won't. The MCP has no upload endpoint. This has been repeatedly confirmed. Queue the human step.
- **"I'll skip the verification — the response said success."** The response lies sometimes. Verification is 2 extra seconds.

---

## Part 10 — When to escalate vs work around

**Work around:** silent style failures, missing asset upload, breakpoint cascade quirks — these are known quirks with known workarounds. Ship.

**Escalate (ask the user):** structural changes to existing sections that would rewrite their Webflow Designer work, publishing to production, deleting CMS collections or pages, changing site settings (SEO, redirects, custom code beyond the standard Head/Body blocks), anything that touches billing or plan.

---

## Appendix: Observed Webflow MCP tool quirks (quick reference)

| Tool | Known quirk | Workaround |
|---|---|---|
| `style_tool.update_style` | Silent rejection of multi-gradient `background-image` on `main` | Inject via helper div or Site Head CSS |
| `style_tool.update_style` | Silent rejection of stacked `drop-shadow` on `main` | Inline style on element |
| `style_tool.update_style` | Silent rejection of `border-left` / shorthand on some classes | Inline style or Site Head CSS |
| `style_tool.update_style` | Silent rejection of `color` / `background-color` on some classes | Inline style on element, or class-forcing CSS in Site Head |
| `style_tool.update_style` | Combining complex + simple properties in one call can fail the simple ones too | Split into separate calls, verify each |
| `asset_tool` | No upload action | Human drags file into Webflow Designer |
| `element_tool.set_text` | Collapses mixed children on some elements | Use structural edits (remove + add children) for headings with spans |
| `data_sites_tool.publish_site` | Publishes immediately, no dry-run | Always read the tree first, verify changes, then publish |
| `whtml_builder` | Accepts raw CSS in `<style>` blocks (no validation) | Use for injected CSS including `@media` rules |

---

This guide is version 1, written after a real site build. It should grow. When a new quirk is hit, add it here before moving on — that's how the pattern library compounds.
