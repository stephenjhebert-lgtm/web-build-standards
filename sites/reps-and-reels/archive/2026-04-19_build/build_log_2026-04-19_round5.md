# Round 5 Build Log — 2026-04-19

**Staging URL:** https://reps-reels-320557.webflow.io/
**Strategy:** Rebuild homepage body from scratch, preserving only the DOM `<style>` element from round 4. Bind all classes at element creation — never via post-creation `set_style`.
**Verdict:** ⚠️ **Partial pass.** 11 of 15 target classes bound at creation. 4 classes (`nav`, `hero`, `footer`, `container`) failed to bind even at creation — same "success then revert" signature, but this time at creation time. Published HTML also shows duplicate `about` + `contact` sections despite a clean Designer state. Deeper Webflow MCP issue — routing decision needed before any further automated build attempts.

Do NOT publish to production.

---

## Step 1 — Clear body ✅

Queried body (`67a9e02b939187f6a007dc26`) children, deleted every child except the DOM `<style>` element (`21e355f7-3ce4-0616-0794-a38a3ebcb071`). Confirmed via re-query: body contained exactly one child, the preserved style element carrying the 14,252-char stylesheet.

## Step 2 — Rebuild 8 sections

All elements created with `set_style.style_names` populated at creation time. No post-creation `set_style` calls were made for any class binding (per the prompt's hard rule). Target element IDs and class outcomes below.

### Landed (classes bound at creation and persisted through publish)

| Section / element | Element ID | Classes that landed |
|---|---|---|
| skip-link `<a>` | `ce9f1f30-e176-8e06-d1ca-7de95f0a245e` | `skip-link` |
| nav inner wrapper | (child of nav) | `nav__inner` |
| hero H1 | (child of hero) | `hero__h1` |
| voice Section | `094cfacc-1ae1-02cd-8602-1d11ba666061` | `voice` |
| voice cornerstone block | (child) | `voice__cornerstone` |
| reels Section | `768ae48d-d718-2956-ac6f-c9b1130107ce` | `reels` |
| reels grid wrapper | (child) | `reels__grid` |
| reel cards (×6) | (grid children) | `reel` |
| reel platform labels (×6) | (card children) | `reel__platform` |
| about inner wrapper | (child of about) | `about__inner` |
| contact H2 | (child of contact) | `contact__h2` |
| footer mark `<img>` | (child of footer) | `footer__mark` |

### Failed to bind at creation (element created, class silently dropped)

| Intended class | Element ID | Element type |
|---|---|---|
| `nav` | `73df6e5e-6e19-3fad-1268-1e8a2516c771` | DOM `<nav>` |
| `hero` | `4a7d0209-d1b2-f343-0c4a-23a91658da73` | Section (`id="main"`) |
| `footer` | `8f782872-3a84-b0a0-9843-84cc2a89de2f` | DOM `<footer>` |
| `container` | 7 DivBlocks (nav__inner wrapper, hero container, voice container, reels container, about container, contact container, footer container) | DivBlock |

Designer query on each confirmed `styleNames: []`. Published HTML confirms — e.g., the nav renders as `<nav>` with no class attribute, the hero as `<section id="main">`, the footer as `<footer>`, and no element carries `container`. Per prompt directive, did not retry.

### Also noted

- **Paragraph-can't-contain-children error** during contact section build: a batched create failed when `contact__body` was a Paragraph with nested TextBlock + TextLink children. Flattened it — `contact__body` Paragraph now uses `set_text`, and `contact__email-link` TextLink is a sibling. The failed batch partially landed before erroring, creating stub about + contact sections that did not clean up on re-query.

## Step 3 — Publish + verify ⚠️

Published to staging subdomain via `data_sites_tool.publish_site`. Verification via curl + regex against `https://reps-reels-320557.webflow.io/?cb=r5`:

```json
{
  "styleBlocks": [6806, 6724, 14326],
  "maxBlock": 14326,
  "skipLink": 1,
  "nav": 0,
  "navInner": 1,
  "hero": 0,
  "heroH1": 1,
  "voice": 1,
  "voiceCornerstone": 1,
  "reels": 1,
  "reelsGrid": 1,
  "reel": 6,
  "reelPlatform": 6,
  "reelImgsBroken": 0,
  "reelImgsTotal": 6,
  "about": 2,
  "aboutInner": 2,
  "contact": 2,
  "contactH2": 2,
  "footer": 0,
  "footerMark": 1,
  "container": 0
}
```

Pass/fail:

| Criterion | Target | Actual | Status |
|---|---|---|---|
| Stylesheet preserved | `maxBlock` ≥ 14,000 | 14,326 | ✅ |
| Reel images | `reelImgsBroken` = 0 | 0 | ✅ |
| 11 BEM classes landed at creation | each ≥1 | each ≥1 | ✅ |
| `nav` class | ≥1 | 0 | ❌ |
| `hero` class | ≥1 | 0 | ❌ |
| `footer` class | ≥1 | 0 | ❌ |
| `container` class | ≥7 | 0 | ❌ |
| Single `about` / `contact` | 1 each | 2 each | ❌ (publish artifact) |

### Duplicate about/contact mystery

Designer state queried post-publish showed exactly 5 Sections on the page (hero, voice, reels, about, contact) and 3 DOM-tag elements (style, nav, footer) — single copies, no duplicates. But published HTML contains two `<section>` blocks matching the about copy and two matching the contact copy. Republishing did not clear them.

Hypothesis: the partially-landed stub sections from the Paragraph-children error (step 2) are still attached at the page/component level somewhere Designer's tree query doesn't surface. Not something the MCP can clean up without a deeper tool (or Steve opening the Designer directly).

## Step 4 — Root cause hypothesis

Four classes failed to bind at creation: `nav`, `hero`, `footer`, `container`. Every one of them is the lowercase form of a Pascal-case class that already exists in the site's Styles panel from rounds 3 and 4 (`Nav`, `Hero`, `Footer`, `Container`). Every BEM class that landed cleanly is unique in the panel (`nav__inner`, `hero__h1`, `voice__cornerstone`, etc.) — no Pascal-case twin.

**Likely cause:** Webflow's class system is case-insensitive for lookup but case-sensitive for storage. When `element_builder` is told to apply class `nav` at creation and a class `Nav` already exists, the API silently drops the binding rather than (a) creating a second class with different casing or (b) reusing the existing Pascal-case one. Same pattern as round 4's "set_style success then revert," just surfacing at creation instead of post-hoc.

This is consistent with round 4's observation that the one successful post-creation binding was `Container` onto an element that already carried other Pascal-case classes — it joined an existing Pascal-case style, which the API accepted. The round 5 attempts asked for lowercase twins of those same Pascal-case classes, which the API cannot produce.

## Remaining work for Steve

**Routing decision needed before more automated build attempts.** Two paths:

1. **Switch the stylesheet and site to Pascal-case classes** for the four collision names (`Nav`, `Hero`, `Footer`, `Container`) — matches what's already in the Styles panel, unblocks MCP creation. Requires updating the CSS in the DOM `<style>` element (selectors `.nav`, `.hero`, `.footer`, `.container` → `.Nav`, `.Hero`, `.Footer`, `.Container`) and re-running section 2 with Pascal-case class names.
2. **Nuke the Pascal-case styles from the Styles panel** so the lowercase names are free, then re-run section 2 as-is. Requires manual Designer cleanup — the MCP has no style-panel-delete endpoint.

Secondary manual items:

- **Duplicate about + contact sections** in published HTML. Open the Designer, confirm the tree shows only one of each, then hard-publish again. If duplicates persist, they may need a direct page re-save from the Designer to force a clean republish.
- **Clear Site Settings → Custom Code → Head Code** (still contains truncated paste from round 3). Not load-bearing — the DOM `<style>` carries the full stylesheet — but worth removing for cleanliness.

Hard stops respected: no production publish, staging only, no components used, no CMS changes, no DNS changes, no Pascal-case classes written (per prompt), no `set_style` retries against failed elements.

---

## What worked, what didn't

**Worked:** Preserving the DOM `<style>` element through a full body clear. Binding classes at element creation for every BEM class with a unique name. Flattening the contact body to avoid the Paragraph-children error. 6 of 6 reel images rendering via CDN.

**Didn't:** Binding the four lowercase-twin-of-Pascal-case classes (`nav`, `hero`, `footer`, `container`) at creation. Cleaning up the partially-landed stub sections that got through during the Paragraph-children error.

**What this means:** The round 4 root-cause hypothesis ("MCP `set_style` can't persist on empty-styleNames elements") was a symptom, not the root cause. The underlying issue is case-insensitive class collision with pre-existing Pascal-case styles. That's structural to the site state from rounds 3–4, not fixable by changing MCP call patterns. Route decision belongs to Steve.
