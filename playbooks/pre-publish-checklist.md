# Pre-publish checklist

Run before any publish. Ship to staging casually; ship to production only after all of this + explicit user go-ahead. Adapted from field guide Part 7.

## Structure check

- [ ] `query_elements` on the home (or changed) page returns the expected tree.
- [ ] No orphaned elements left from editing sessions.

## Style check

- [ ] `query_styles` on every class you edited this session shows the values you intended.
- [ ] Any `main`-breakpoint updates have been verified, not assumed.

## Content check

- [ ] No placeholder text (`Lorem...`, `[ placeholder ]`, `TODO`) in production-visible sections.
- [ ] No `href="#"` or `href="javascript:void(0)"` left behind.
- [ ] All images have `alt` text (from the asset record or element override).

## Workaround audit

- [ ] Every inline-style workaround applied this session is recorded in the site's `source-of-truth.md`.
- [ ] Every helper div / Site Head CSS change is recorded.
- [ ] If any new Webflow MCP quirk was hit, it's been appended to `standards/webflow/mcp-field-guide.md`.

## Publish

- [ ] Staging publish: `data_sites_tool.publish_site` with `publishToWebflowSubdomain: true`.
- [ ] Load the staging URL in an **incognito** browser tab to bypass caching.
- [ ] Scroll through, compare to reference.

## Production (only on explicit go-ahead)

- [ ] Steve said "publish to production" in the current turn.
- [ ] All of the above has passed on staging.
- [ ] `data_sites_tool.publish_site` to `customDomains`.
- [ ] Post-publish incognito check on the live URL.
