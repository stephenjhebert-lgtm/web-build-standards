# Claude Code — Round 2 Fix Prompt (Reps & Reels)
**Paste everything below this line into a fresh Claude Code session.**

---

You previously built the Reps & Reels site at `https://reps-reels-320557.webflow.io/` following `/Reps and Reels/web/claude_code_build_brief.md`. Structure, copy, and nav are correct. **Styling is not.** Below the voice moment section, most of the page renders with browser defaults — no typography scale, no container padding, no reels grid, no card styling, no footer layout. The reels grid collapsed to a vertical stack of tiny blue play-icon SVGs.

Root cause: the global stylesheet from `homepage_prototype.html` never made it to Webflow. Fix it.

## Reading order (skim, then execute)

1. `/Reps and Reels/web/staging_qa_fix_list.md` — the full QA pass, what broke, what's expected, severity-ordered
2. `/Reps and Reels/web/homepage_prototype.html` lines 57–609 — the stylesheet you need to paste
3. `/Reps and Reels/web/design_direction.md` — the design system (for class-naming reference)
4. `/Reps and Reels/web/reels_embed_spec.md` §5 — reel card markup structure

## Confirm prerequisites

- Webflow MCP connected (`/mcp`)
- Bridge App running in Webflow Designer at `https://reps-reels-320557.design.webflow.com`
- Site ID captured

If any of those fail, stop and ask Steve.

## Execution sequence

### Step 1 — Paste the global stylesheet into Site Settings → Custom Code → Head

Extract lines 57–609 of `/Reps and Reels/web/homepage_prototype.html` (the entire `<style>...</style>` block, opening and closing tags included). Paste verbatim into **Webflow Site Settings → Custom Code → Head Code** using the MCP custom-code endpoint.

If the MCP tool for head custom code doesn't work, stop and report. Do not fall back to per-page embeds — this needs to be site-wide.

### Step 2 — In Designer, add Webflow class names so the stylesheet selectors hit

The pasted CSS expects these class names. Add them to the corresponding Webflow elements via MCP Designer API:

| Element | Class name(s) |
|---|---|
| Every major section wrapper | `section` |
| Inner content wrapper (max-width 1280px, edge padding) | `container` |
| Hero section specifically | `hero` |
| Hero eyebrow chip ("RR · THE OVERLAP") | `eyebrow` |
| Hero H1 ("Two hobbies...") | `h-display-xl` |
| Hero subhead | `body-lg` |
| Voice moment pullquote wrapper | `pullquote` |
| Reels section grid wrapper | `reels-grid` |
| Each reel card link | `reel-card` |
| Reel media wrapper inside card | `reel__media` |
| Reel meta wrapper inside card | `reel__meta` |
| Platform chip inside meta | `reel__platform` |
| Caption paragraph inside meta | `reel__caption` |
| About section H2 ("About") | `h-display-md` |
| About body paragraph | `body-lg` |
| Contact section H2 ("Hit me.") | `h-display-md` |
| Contact body | `body-lg` |
| Contact list (IG / TikTok / email) | `contact-list` |
| Email signup wrapper | `email-signup` |
| Footer | (footer element uses `footer`-prefixed classes — match by selector intent) |
| Skip link | `skip-link` |

Reference `homepage_prototype.html` for the exact markup — every element has a class in the prototype. Match Webflow classes to prototype classes 1:1.

### Step 3 — Fix the reels grid markup

Currently each card renders as a plain `<a>` wrapping a generic play-icon SVG. Per `reels_embed_spec.md` §5, each card needs this structure:

```html
<a class="reel-card" href="[post-url]" data-embed-url="[embed-url]" target="_blank" rel="noopener">
  <div class="reel__media">
    <!-- placeholder or embedded iframe -->
  </div>
  <div class="reel__meta">
    <span class="reel__platform">IG</span>
    <p class="reel__caption">[caption snippet]</p>
  </div>
</a>
```

Rebuild the 6 placeholder cards in Designer with this structure.

### Step 4 — Fix hero padding

Currently the hero has ~500px of empty black above the eyebrow chip. Target: eyebrow visible 120-160px from top of section. Remove any spacer divs or excessive min-height / top-padding. The pasted `.hero { padding: clamp(80px, 12vw, 160px) 0; }` rule should handle this once the section has class `hero`.

### Step 5 — Fix Rotating subtitle inline link

Currently: "Recent. The rest is on IG and TikTok — on both." with `@repsreels` pulled out as a separate link above.
Target: "Recent. The rest is on IG and TikTok — @repsreels on both." as a single sentence with `@repsreels` as an inline link to Instagram.

### Step 6 — Verify

Load `https://reps-reels-320557.webflow.io/` in a browser. Scroll top to bottom. Every section should match `homepage_prototype.html` visually:
- Hero: eyebrow ~120-160px from section top, H1 in Big Shoulders Display, subhead in Fustat
- Voice moment: paragraph + 4px brass left-ruled pullquote in IBM Plex Mono
- Rotating: 3-column grid of 9:16 cards on desktop, 2-column on tablet, 1-column on mobile. Brass platform chip top-right of each card.
- About: single short paragraph max 32ch, Bone Paper background
- Hit me.: styled list of IG / TikTok / email links
- Footer: standalone manifesto mark centered, tagline, social list, copyright

If anything below the hero is still unstyled (default fonts, flush-left text, no padding), class names in Designer don't match the pasted CSS selectors. Fix before reporting complete.

### Step 7 — Report back

Write to `/Reps and Reels/web/build_log_2026-04-19_round2.md`:
- What was pasted (line count of CSS block)
- Which Webflow classes were renamed or added
- Which reel card markup was rebuilt
- Hero padding change
- Subtitle link fix
- Screenshots or descriptions of each section after the fix
- Any steps that failed + why

Then STOP. Do not publish to production. Do not publish to staging unless required by the MCP workflow — if staging publish is required, publish staging only.

## Hard stops (unchanged from round 1)

- ❌ No production publish
- ❌ No DNS changes
- ❌ No content deletion without backup
- ❌ No copy rewrites
- ❌ No design improvisation — follow `design_direction.md` + `homepage_prototype.html`

## Success criteria

Round 2 is complete when staging at `reps-reels-320557.webflow.io` matches the prototype visually across hero, voice moment, rotating, about, hit me, and footer — at desktop, tablet (768px), and mobile (375px). Steve will do the final visual QA before Phase 2.

Start with Step 1. Report at each step boundary using the `[Step X] DONE — <summary>. Next: <step>.` format.
