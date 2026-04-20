# Round 6 Build Log — 2026-04-19

**Staging URL:** https://reps-reels-320557.webflow.io/?cb=r6clean
**Strategy:** Nuke everything old. Clean build. Per user directive: "anything that's old should be removed. full stop."
**Verdict:** ✅ **Full pass.** All 4 collision classes (`nav`, `hero`, `footer`, `container`) bound at element creation. No duplicate sections. All target BEM classes landed. First clean pass across 6 rounds.

Do NOT publish to production. Staging verification only.

---

## Root cause (confirmed in round 5) and fix (round 6)

Round 5 hypothesized case-insensitive style-name collision with Pascal-case classes (`Nav`, `Hero`, `Footer`, `Container`) that shipped with the Evolux clone template. Round 6 confirmed it: creating lowercase `nav` while `Nav` existed returned `"Cannot have duplicate style names"` — the name space is case-insensitive for uniqueness. The only path to lowercase was to remove the Pascal-case twins, which meant releasing every element that pinned them.

---

## Step 1 — Body cleanup ✅

Queried Home body, deleted all 10 prior-round children (skip-link, nav, hero, voice, reels, about×2, contact×2, footer). Preserved the DOM `<style>` element (`21e355f7-3ce4-0616-0794-a38a3ebcb071`) carrying the 14,252-char stylesheet.

## Step 2 — Style panel sweep ✅

### rr-* legacy (rounds 3/4) — 47 removed

All `rr-*` prefixed styles removed cleanly. The one oddity: `rr-voice__quote-line--brass` existed as both a standalone style and a combo class (same name, two IDs). First removal took the combo, second took the standalone.

### Template-page pins — released via body clears

Four Evolux template pages (404, 401, Licenses, Changelog) pinned ~15 Pascal-case styles. Removed their root body blocks in turn:
- **404 page** (`69322470da6d6f12bcacc3bc`) — deleted `404 Content` block
- **401 page** (`69322470da6d6f12bcacc3bd`) — deleted `Utility Page Wrap` block
- **Licenses page** (`69322470da6d6f12bcacc3bf`) — deleted `Utility Content` block
- **Changelog page** (`69322470da6d6f12bcacc3c0`) — deleted `Utility Content` block

Template pages are now empty. The routes still exist (`/404`, `/401`, `/utility-pages/licenses`, `/utility-pages/changelog`) — they render blank. Steve can rebuild or leave as-is.

### Component-pins — released via unregister

Seven registered components from the Evolux clone still held the Nav/Footer/Container/Logo/Chips styles after page clears:
- `Nav` (Navigation group)
- `Footer` (Navigation group)
- `Logo` (Navigation group)
- `Socials` (Blocks group)
- `Symbol` (Blocks group, description "Socials")
- `FAQ Item` (Blocks group)
- `Text Block` (Blocks group)

All seven unregistered via `de_component_tool.unregister_component`. This was the missing step in rounds 3–5 — the Pascal-case styles survived every page-level cleanup because they were pinned at the component-definition level.

### Pascal-case styles removed — ~90 total

All 4 collision classes: `Nav`, `Hero`, `Footer`, `Container` ✅

Plus the rest of the Evolux template cruft: all Icon 10/12/16/18/20, Icon Block/Blocks/Content/Paragraph/Wrapper, Button/Button Icon/Button Wrapper/Buttons Wrapper/Form Button/Form Button Wrapper, Form/Form Block, H1/H2/H3 (Pascal), Switcher Button/Switcher Code, Light Mode/Light Mode Icon/Dark Mode/Dark Mode Icon, Benefit Block/Blocks/Content/Icon, Info Block/Blocks/Content/Icon, FAQ Answer/Answer Text/Column/Content/Icon/Question/Question Text/Wrapper, Proof/Proof Image/Proof Images, Hero Content/Hero Main/Hero Offer/Hero Offer Wrapper/Hero Paragraph/Hero Text/Hero Utility/Hero Info, About Content/Text/Text Wrapper/Wrapper/Info, Contact Content/Heading/Info/Paragraph/Socials/Text/Wrapper, Text Block/Block Paragraph/Blocks/Link, Highlighted Text, Primary/Secondary, Sections List, Social Buttons, Footer Content, Medium L/M/S, First Block/Second Block/Third Block/First Image, Utility Content/Page Wrap/Page Form/Page Content, 404 Content/Hero/Hero Wrapper/Hero Content, Utility Hero Wrapper, Video/Video And Ticker/Video Paragraph, Ticker/Ticker Line/Ticker Logo/Ticker Wrapper, Chips Icon, Section, Success Message/Error Message, Play Button, Input, Logo Symbol, ig-feed.

### Residual styles that couldn't be removed

A small handful returned "Failed to remove style. Ensure there are no usages of this style." even after the component unregister sweep:
- `Icon Button`, `Logo`, `Chips`, `Video Wrapper`, `FAQ Item`, `Hero Wrapper`, `About Info`, `Text Blocks`

These are pinned somewhere the Designer tree doesn't expose (possibly orphan component fragments or internal Webflow references). None of them conflict with target BEM classes — they're just stale entries in the Styles panel. Not blocking. Steve can hunt them down manually in the Designer if he wants a 100%-empty panel; otherwise they're cosmetic.

## Step 3 — Create the 4 collision classes ✅

With Nav/Hero/Footer/Container removed, creating lowercase twins succeeded:
- `nav` → style ID `9a20a473-d9ce-8421-3518-484c97b064f1`
- `hero` → style ID `46080243-d65a-9e28-256f-23e6693e6911`
- `footer` → style ID `fba6a642-4c9d-9914-d9b5-7a1aae59177e`
- `container` → style ID `834ce41c-3113-2d3b-8562-0f33d40cb990`
- `body-lg` → style ID `f88f918f-acd1-71db-50c3-890ac3647eff`
- `body-sm` → style ID `3c2b8b77-2710-64a3-cf21-a9dfcf40cb1a`

All created as empty Webflow styles. The real CSS is still carried by the DOM `<style>` element — these Webflow styles exist purely as class-name anchors so `element_builder` can bind them.

## Step 4 — Rebuild 8 sections ✅

Every class bound at element creation via `set_style.style_names` in `element_builder`'s `element_schema`. No post-creation `set_style` calls for initial bindings.

### Element IDs landed

| Section | Element ID | Classes bound |
|---|---|---|
| skip-link (TextLink) | `0efeb826-d2f7-c9ee-4475-a8f1e5c29b74` | `skip-link` |
| nav (DOM nav) | `6fc582f1-5a97-0a92-093c-6b872a95cc0a` | `nav` |
| nav__inner (child) | nested | `container`, `nav__inner` |
| hero (Section id=main) | `63e4f0a3-e4ed-a17d-e641-08c54e554415` | `hero` |
| hero__bg (child) | nested | `hero__bg` |
| hero__content (child) | nested | `container`, `hero__content` |
| hero__chip (TextBlock) | nested | `mono`, `hero__chip` |
| hero__h1 (Heading h1) | nested | `hero__h1` |
| hero__subhead (Paragraph) | nested | `subhead`, `hero__subhead` |
| voice (Section) | `25fda027-36ee-89ad-65db-c8266ef0c709` | `voice` |
| voice__inner (child) | nested | `container`, `voice__inner` |
| voice__body, voice__cornerstone | nested | per class |
| reels (Section) | `3ee1d6ee-546f-b486-24cd-d44dfc0a990f` | `reels` |
| reels container, header, grid, 6 reel cards | nested | `container`, `reels__header`, `reels__grid`, `reel` ×6, `reel__platform` ×6, `reel__placeholder` ×6, `reel__caption` ×6, `reels__label`, `reels__fallback-note`, `body-sm`, `mono` |
| about (Section) | `2e0ebce9-d1df-ae38-7d82-c5ec0cfe0a43` | `about` |
| about__inner, about__label, about__heading, about__body | nested | per class |
| contact (Section) | `12e13d88-3cf4-395b-2a5a-f80b989a6f35` | `contact` |
| contact inner, label, h2, body, email-link, form-block, form-copy, form-placeholder | nested | per class |
| footer (DOM footer) | `bc44c95c-11eb-4bb8-1de2-7b233cf407ed` | `footer` |
| footer container, top, row, wordmark, mark, tagline, social, email, copy | nested | per class |

### Adjustments vs prototype

- **SVG icons replaced with text labels** ("IG", "TT"). Webflow element types don't include raw SVG and embedding it via DOM would have required extra builder calls. The link targets and aria-labels match prototype — visual icons are a post-build polish item.
- **Reel placeholder content** matches prototype exactly — 6 cards, each with platform label, `[ Reel embed ]` placeholder block, and caption text. No image bindings (prototype doesn't bind images at this stage — FPO image binding was a round-4-only artifact).
- **No paragraph-children crashes.** Structured `contact__body` as a Paragraph with `set_text` and made `contact__email-link` a TextLink sibling. Same flattening applied to the footer's email link.

## Step 5 — Publish + verify ✅

Published via `data_sites_tool.publish_site` to `publishToWebflowSubdomain: true`. Verification against `https://reps-reels-320557.webflow.io/?cb=r6clean`:

```json
{
  "styleBlocks": [6806, 6724, 14326],
  "skipLink": 1,
  "nav_class": 1,
  "navInner": 1,
  "hero_class": 1,
  "heroH1": 1,
  "voice": 1,
  "voiceCornerstone": 1,
  "reels": 1,
  "reelsGrid": 1,
  "reel": 6,
  "reelPlatform": 6,
  "about": 1,
  "aboutInner": 1,
  "contact": 1,
  "contactH2": 1,
  "footer_class": 1,
  "footerMark": 1,
  "container": 7,
  "htmlLen": 43188
}
```

### Pass/fail table

| Criterion | Target | Actual | Status |
|---|---|---|---|
| DOM stylesheet preserved | `maxBlock` ≥ 14,000 | 14,326 | ✅ |
| `nav` class (collision) | 1 | 1 | ✅ |
| `hero` class (collision) | 1 | 1 | ✅ |
| `footer` class (collision) | 1 | 1 | ✅ |
| `container` class (collision) | 7 | 7 | ✅ |
| All 14 BEM bindings | each ≥1 | each ≥1 | ✅ |
| 6 reel cards | 6 | 6 | ✅ |
| Duplicate about/contact | 1 each | 1 each | ✅ |

**Every criterion passed.** First clean build across 6 rounds.

---

## What changed from round 5 to unblock this

1. **Template page clears.** The 404/401/Licenses/Changelog pages each pinned Pascal-case styles via their body content. Clearing those bodies released the page-level pins.
2. **Component unregister.** Seven Evolux template components held the Nav/Footer/Container/Logo/Chips styles at the component-definition level — invisible to page-level queries. Unregistering all seven was the missing step.
3. **Create-style after remove.** Once `Nav`/`Hero`/`Footer`/`Container` were gone, `create_style` with lowercase names succeeded, unblocking `element_builder.set_style` for those classes at creation time.

Round 5's hypothesis was correct: case-insensitive name collision was the blocker. The fix required page + component cleanup, not just page cleanup.

## Manual items for Steve (non-blocking)

1. **Stale styles panel entries** — ~8 Pascal-case styles (`Icon Button`, `Logo`, `Chips`, `Video Wrapper`, `FAQ Item`, `Hero Wrapper`, `About Info`, `Text Blocks`) that resist removal via MCP. Likely orphan component fragments. Not affecting build; cosmetic cleanup only.
2. **Template pages are empty** — 404/401/Licenses/Changelog render blank. Rebuild if/when needed.
3. **SVG icons** — nav and footer use `IG`/`TT` text placeholders instead of prototype SVGs. Webflow element model doesn't expose SVG elements cleanly; better done via Custom Code embed or Image asset. Non-blocking for structure validation.
4. **Head Code field still contains truncated paste from round 3** — DOM `<style>` supersedes it, but Steve can clear it in Site Settings for cleanliness.

Hard stops respected: no production publish (staging only), no DNS changes, no CMS, no Pascal-case classes used in the build, all classes lowercase BEM.
