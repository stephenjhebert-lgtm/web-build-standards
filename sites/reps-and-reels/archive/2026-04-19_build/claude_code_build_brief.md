# Claude Code Build Brief — Reps & Reels Website Refresh
**Version:** 1.0
**Date:** 2026-04-19
**Target:** Claude Code (autonomous execution with Webflow MCP)
**Estimated runtime:** 45-90 minutes of agent time, walk-away-safe within defined guardrails

---

## How to use this document

This file is the **single entry point** for a Claude Code session that will rebuild the Reps & Reels website in Webflow using the Webflow MCP server. Paste this full document into a fresh Claude Code session as the opening prompt.

Do not modify the file order, remove sections, or skip steps. Every section exists because something can go wrong if it's missing.

---

## 1. Mission

Rebuild `reps-reels-320557.design.webflow.com` according to the approved specs in `/Reps and Reels/web/`. Build to **staging only**. Do not publish to production. Do not touch DNS. Report when done and wait for Steve's approval before any final publish.

The site is a personal content brand (creator / lifter / bagpiper) for Steve Hebert. The brand voice is **humble, dry, self-aware, understated**. Any content drift toward motivational clichés, hype language, or AI-sounding filler is a failure state. If in doubt, quote the brand voice guide directly.

---

## 2. Operator context

You are not starting from zero. Every design decision, every line of copy, every technical spec has been approved. Your job is **execution**, not authorship. If you feel the urge to improve, rewrite, or reinterpret the specs — don't. Quote them and move on.

If a spec is ambiguous or missing detail, **stop and ask Steve** rather than improvising. Ambiguity surfaced as a question is better than ambiguity resolved by guesswork.

---

## 3. Prerequisites — confirm before starting

Check each of these. If any fails, stop and report back.

### 3.1 MCP connection

Run `/mcp` in Claude Code. Confirm the Webflow MCP server is listed and connected. If not:

```bash
claude mcp add --transport http webflow https://mcp.webflow.com/mcp -s project
```

Then reload Claude Code, run `/mcp`, select Webflow, and complete OAuth.

**If OAuth fails or the server doesn't appear, STOP.** Report: "Webflow MCP not connected. Need Steve to authorize."

### 3.2 Webflow Skills package (optional but recommended)

Install Webflow's pre-built skills for common tasks:

```bash
npx skills add webflow/webflow-skills
```

This gives you access to `cms-collection-setup`, `bulk-cms-update`, and related flows. If the install fails, continue without it — fall back to direct MCP tool calls.

### 3.3 Bridge App

Designer-API-level work (layout, styling, section creation) requires Webflow's **MCP Bridge App** to be open in the Designer. Ask Steve to confirm:

> "Steve — please open https://reps-reels-320557.design.webflow.com in Webflow Designer, press `E` to open the Apps panel, and launch 'Webflow MCP Bridge App'. Confirm when it's running."

**Do not proceed with any Designer-level work until Steve confirms Bridge App is running.** Data-API-only work (CMS, custom code, SEO metadata) can proceed without it.

### 3.4 Site access

Call `list_sites` or equivalent MCP tool. Confirm you can see `reps-reels-320557` (or the current R&R site — match by domain `repsreels.com`). Capture and hold the site ID for the rest of the session.

If access fails: stop, report, ask Steve to re-authorize OAuth with the correct site selected.

---

## 4. Hard stops — non-negotiable

These actions require **explicit approval from Steve in the current session**. Do not take them autonomously under any circumstance, even if the specs imply them:

1. **Publishing to production** (repsreels.com). You may publish to staging (webflow.io subdomain) freely. Production publish is Steve's action only.
2. **DNS changes** of any kind. The brief says DNS is untouched for this launch.
3. **Deleting the existing Webflow project** or any of its backups.
4. **Deleting content** without creating a backup first (e.g., the current FAQ section should be removed, but back up the page/section data first).
5. **Installing or modifying OAuth scopes** beyond what's already authorized.
6. **Publishing content to Omnisend, Instagram, TikTok, or any external system.**

If you encounter a situation that requires any of the above, **STOP and ask**. Do not assume.

---

## 5. Reading order — specs to consume before acting

All files are in `/Users/stephenhebert/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/Reps and Reels/web/`.

Read these in this exact order. Do not skim — these are short and load-bearing.

1. **`verification_report.md`** — Current state of all deliverables. Flags open issues (OG image blocker, canonical typo). Start here so you know what's clean vs. what's pending.
2. **`homepage_copy.md`** — Final copy for every section. **Verbatim.** Do not rewrite, do not "improve."
3. **`design_direction.md`** — Palette, typography, type scale, motion, layout, a11y. This IS the design system.
4. **`image_specs.md`** — What goes in each image slot. Existing assets in `/Reps and Reels/Assets/`.
5. **`seo_package.md`** — Title, meta description, canonical, JSON-LD schema, OG tags, favicon markup.
6. **`reels_embed_spec.md`** — CMS Collection schema (§2), embed HTML template (§5), custom code JS (§6) + CSS (§7).
7. **`launch_checklist.md`** — The full build sequence. Phase 1 is your job. Phases 2-4 are partly yours, partly Steve's.
8. **`homepage_prototype.html`** — Reference implementation. Use this as a visual target for the Designer work. Every section, spacing, color, and interaction in this file should match what you build in Webflow.

Also reference when needed:
- **`/Reps and Reels/Brand/brand-voice-guide.md`** — Voice rules. Quote from this if any content decision is in doubt.
- **`/Reps and Reels/Brand/brand_overview.md`** — Brand positioning.
- **`/Reps and Reels/Assets/`** — All image assets (brand mark, sticker, candlelit illustration, etc.).

---

## 6. Execution sequence

Follow the phases in `launch_checklist.md` Phase 1 exactly. Summary with MCP tool mapping below.

### Phase 1.1 — Pre-build prep
- Use MCP to list sites, confirm site ID.
- Ask Steve to manually create a Webflow backup via Designer (MCP cannot trigger backups). Wait for confirmation.

### Phase 1.2 — Strip the current site
- Remove dark mode toggle, FAQ section, current hero content.
- **Before deleting anything, export or document it** via the appropriate MCP read tool so there's a record.

### Phase 1.3 — Fonts
- Add Big Shoulders Display (800, 900), IBM Plex Mono (500, 600). Keep Fustat (400, 500, 600).
- If MCP doesn't expose font management, this step may require Designer UI. Flag it and continue.

### Phase 1.4 — Color variables
- Create global color swatches per `design_direction.md` §1. Use the exact names and hex values. Use Webflow design variables / CSS custom properties.

### Phase 1.4.5 — Paste global stylesheet into Site Settings → Custom Code → Head ⚠️ DO NOT SKIP
**This step prevents the failure mode from round 1 where 80% of the page rendered unstyled.**

Extract lines **57–609** of `/Reps and Reels/web/homepage_prototype.html` — the entire `<style>...</style>` block including the opening and closing tags. Paste it verbatim into **Webflow Site Settings → Custom Code → Head Code** via the MCP `Custom Code` endpoint (or, if MCP can't write there, ask Steve to paste it manually).

This stylesheet defines every class name the DOM needs: `.section`, `.container`, `.hero`, `.h-display-xl`, `.h-display-md`, `.eyebrow`, `.body-lg`, `.reels-grid`, `.reel-card`, `.reel__media`, `.reel__meta`, `.reel__platform`, `.reel__caption`, `.pullquote`, `.email-signup`, `.skip-link`, footer classes, and more. Without it, Webflow falls back to browser defaults for every section that doesn't have a Webflow-native class override — which is most sections.

**Then, in Designer, add the matching class names to the actual elements** so the CSS selectors in the pasted stylesheet hit them. Webflow class names are case-sensitive and hyphen-delimited.

After this step, before proceeding: load the staging URL in a browser, scroll top to bottom, and confirm About / Hit me / Contact / Footer render with the correct typography scale and container padding. If they don't, the class names in Designer don't match the CSS selectors in the pasted stylesheet. Fix before moving on.

### Phase 1.5 — Sections
Build in this order. Use `homepage_prototype.html` as the reference implementation:

1. **Nav** — round patch logo (28px, `rr_sticker.png`) + wordmark, IG + TikTok social icons right
2. **Hero** — 100vh, brand mark background element, mono chip, H1 ("Two hobbies nobody asked me to combine."), subhead
3. **Voice moment** — paragraph + cornerstone pull-quote ("The years are invisible. The result isn't.")
4. **Reels grid** — see §6.6 (complex, use CMS skill if available)
5. **About / origin** — Bone Paper background, single paragraph max 32ch
6. **Contact** — "Hit me." heading, mailto link, Omnisend form embed placeholder
7. **Footer** — full manifesto mark (`brand_mark_primary_gold.png`, ~140-160px), tagline, social icons, email, copyright

Typography, spacing, and motion all per `design_direction.md`. Verify mobile (375px), tablet (768px), desktop (1280px+) at each section.

### Phase 1.6 — Reels CMS (the complex one)

If the `cms-collection-setup` Webflow skill is installed, trigger it with:

> "Create a CMS Collection called 'Reels' with fields: Name (plain text, required), Platform (option: Instagram/TikTok, required), Post URL (link, required), Caption snippet (plain text, max 80 chars), Date posted (date, required), Featured (switch), Hide from homepage (switch)."

Otherwise, use direct MCP tool calls per `reels_embed_spec.md` §2.

After the Collection exists:
- Seed **6 Reels items** with real data. Ask Steve for the 6 most recent IG and TikTok post URLs, or use placeholder items with the structure documented in §5 of the embed spec.
- Place a Collection List in the reels section of the homepage, bound to the Reels Collection. Sort: Featured desc, Date posted desc. Limit: 6. Filter: Hide from homepage = false.
- Inside each Collection Item, use an Embed block with the HTML template from `reels_embed_spec.md` §5.

### Phase 1.7 — Omnisend form
- Port the existing Omnisend embed code from the current site into the Contact section.
- Apply scoped styling per `launch_checklist.md` §1.7.
- Test at mobile width.

### Phase 1.8 — Head tags + SEO
Apply the full SEO package via MCP:
- Page title and meta description per `seo_package.md` §1-2
- Canonical URL → `https://www.repsreels.com/` (**critical: verify it doesn't inherit the `repsandreels.com` typo from the current site**)
- Person JSON-LD schema per `seo_package.md` §4 — inject via site-wide custom code (head)
- OG tags per §5 — per-page
- theme-color meta
- Favicon (use `rr_sticker.png`), Apple touch icon (use `brand_mark_primary_gold.png`)

**OG image blocker:** per `verification_report.md`, the real OG image has not been produced yet (requires manual Figma/Canva work). For staging, use `brand_mark_primary_gold.png` as a placeholder. **Flag this for Steve before final publish** — do not ship to production with a placeholder OG.

### Phase 1.9 — Custom code injection
Per `reels_embed_spec.md` §6-7, inject:
- Reel loader JS → footer custom code (site-wide)
- Reel card CSS → head custom code (site-wide)
- Plausible script → head custom code: `<script defer data-domain="repsreels.com" src="https://plausible.io/js/script.js"></script>`
- Person JSON-LD → head custom code

Use MCP Custom Code endpoints: `PUT /v2/sites/:site_id/custom_code` or equivalent. If the MCP tool surface doesn't expose custom code injection, flag it and drop the raw code into the Webflow UI's Custom Code panel — ask Steve to paste.

### Phase 1.10 — Strip old stuff
- Remove old analytics scripts
- Remove old font loaders
- Confirm total custom JS under 15KB (excluding Plausible + Omnisend which load externally)

---

## 7. Reporting — checkpoint format

At the end of each major phase (1.1, 1.2, ..., 1.10), write a one-line status to the Claude Code session:

```
[Phase 1.X] DONE — <brief summary>. Next: <next phase>.
```

If a phase is blocked, write:

```
[Phase 1.X] BLOCKED — <what's blocking>. Need from Steve: <specific ask>.
```

Do not move past a blocked phase without either Steve's response or an explicit decision to skip and flag.

---

## 8. Final report at end of session

When Phase 1 is complete (or blocked at a point that requires Steve), write a summary to `/Reps and Reels/web/build_log_YYYY-MM-DD.md` containing:

- What was built, section by section
- What CMS items were created
- What custom code was injected, with byte counts
- Any MCP tool calls that errored + how they were resolved
- Any specs that were ambiguous + how you interpreted them (or escalated)
- Staging URL for Steve to review
- **Explicit list of anything NOT done and why** (e.g., OG image placeholder, Omnisend form styling pending)
- Link to next phase (Phase 2 QA in `launch_checklist.md`)

---

## 9. Guardrails summary (one-screen reference)

- ✅ Build to staging (Webflow `.webflow.io` subdomain)
- ❌ Do NOT publish to production (`repsreels.com`)
- ❌ Do NOT touch DNS
- ❌ Do NOT delete anything without backing up
- ❌ Do NOT rewrite approved copy
- ❌ Do NOT improvise visual choices — follow `design_direction.md`
- ❌ Do NOT ship the placeholder OG image to production
- ❌ Do NOT add analytics beyond Plausible
- ❌ Do NOT install unauthorized third-party scripts
- ✅ STOP and ask Steve when in doubt
- ✅ Quote specs verbatim when resolving ambiguity
- ✅ Write build log at end of session

---

## 10. Success criteria

Phase 1 is **done** when:

1. All 7 homepage sections exist in Webflow staging and match `homepage_prototype.html` visually
2. Reels CMS Collection exists with 6 seeded items, rendering in the grid
3. All custom code injected (JSON-LD, Plausible, reel loader JS/CSS)
4. All SEO metadata set (title, description, canonical, OG, Twitter, favicon, theme-color)
5. Canonical verified to point at `repsreels.com` (not the typo)
6. Staging URL loads cleanly on mobile + desktop, no console errors
7. Build log written
8. Steve has the staging URL and can run Phase 2 QA

---

## 11. If something goes wrong

**MCP tool errors:**
- Check if the Bridge App is still running (required for Designer-level tools)
- Check Webflow rate limits (1 publish per minute, others not documented)
- Re-authenticate if auth errors

**Webflow CMS quirks:**
- Field slugs are auto-generated from field names (lowercase, hyphens). Don't fight this — reference slugs from the Collection config after creation.
- Collection Items created via API default to draft — use `/items/live` endpoint to create and publish in one call.

**If the spec is wrong or outdated:**
- Stop, explain what you found, and ask Steve.
- Do not silently correct the spec. The spec is the source of truth for this build; if it's wrong, Steve needs to know before anything else ships.

**If you can't complete Phase 1 autonomously:**
- That's fine. Ship what you can, document what you couldn't, and hand back to Steve with specifics.

---

## 12. After Phase 1 — what's next

Phase 1 is handoff. Once Steve reviews staging:

- **Phase 2 (QA)** — can be driven by you in a later session, but requires Steve running Lighthouse on his machine
- **Phase 3 (go live)** — Steve's call, Steve's publish action
- **Phase 4 (post-launch)** — GSC submission, social bio updates, monitoring

See `launch_checklist.md` for the full downstream sequence.

---

## 13. Reusability note (for future sessions)

This brief is the R&R instance of a generalized template at `/Users/stephenhebert/Library/Mobile Documents/com~apple~CloudDocs/Claude/Projects/RepsReels/webflow_build_template.md`. For a future site build, clone the template, fill in the site-specific variables, and point Claude Code at the new brief. The 13-section structure here stays the same.

---

## End of brief

When you've read all the spec files and confirmed prerequisites, start with Phase 1.1. First output to Steve should be:

```
Ready to build. Confirmed:
- MCP connected ✓
- Skills installed ✓ / ✗
- Bridge App running ✓ / ✗
- Site access confirmed: <site-id>
- Target: staging (webflow.io subdomain)
- Estimated time: <X> minutes

Starting Phase 1.1. Will report back at each phase boundary.
```

Then execute.
