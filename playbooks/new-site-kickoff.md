# New-site kickoff playbook

Run this before touching Webflow for any new site. Each item should take under 5 minutes. Drawn from the field guide's Part 8 pre-flight checklist.

## 1. Capture site identity

- [ ] Create the site in Webflow (or confirm it exists).
- [ ] Copy the **site ID** and paste it into the local `source-of-truth.md` (see `templates/site-source-of-truth-template.md`).
- [ ] Note any component IDs you'll reference often.

_Field guide ref: Part 6.2 (local state files)._

## 2. Set up local files

Copy these templates into the site's project folder:

- [ ] `templates/site-claude-md-template.md` → `./CLAUDE.md` (fill Brand, Site Name)
- [ ] `templates/site-source-of-truth-template.md` → `./source-of-truth.md`
- [ ] `templates/webflow-head-paste-template.html` → `./webflow_head_paste.html`

- [ ] Add this site to `sites/<brand>.md` in the `web-build-standards` repo.

## 3. Plan the breakpoint strategy

- [ ] Confirm which breakpoints the design targets: desktop-only, responsive, mobile-priority.
- [ ] Map out the `main` → `medium` → `small` → `tiny` cascade before touching styles.

_Field guide ref: Part 4 (breakpoint cascade)._

## 4. Queue the asset handoff

- [ ] List every asset the design needs.
- [ ] Identify which are not yet in Webflow Assets.
- [ ] Hand Steve the list of files to drag in **now**, not mid-flow.

_Field guide ref: Part 3 (asset management)._

## 5. Prepare the custom-code sync

- [ ] Paste the contents of `webflow_head_paste.html` into Webflow Site Settings → Custom Code → Head.
- [ ] Commit to editing the local file first, then re-pasting. Never hand-edit in Webflow.

_Field guide ref: Part 2.4 + Part 6.2._

## 6. Define the verification strategy

- [ ] After every `style_tool.update_style` on `main`, plan to call `query_styles` to confirm persistence.
- [ ] After every publish, load the staging URL in incognito to bypass caching.

_Field guide ref: Part 1.2, Part 6.3, Part 7.3._

## 7. Set publish cadence expectations

- [ ] Staging publishes: frequent, low-ceremony.
- [ ] Production publishes: only on explicit "publish to production" from Steve in the current turn.

_Field guide ref: Part 7.1._

## 8. Document workarounds as they happen

- [ ] Every inline-style override, every injected helper div, every "this had to go in Site Head because style_tool rejected it" — log it in `source-of-truth.md` and (if novel) in `standards/webflow/mcp-field-guide.md`.
