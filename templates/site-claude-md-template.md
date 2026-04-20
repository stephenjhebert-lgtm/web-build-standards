# CLAUDE.md — [Site Name]

**Purpose:** Site-specific overrides and context for this Webflow build. Use in combination with the global standards at:
https://github.com/stephenjhebert-lgtm/web-build-standards

**Read order for any task in this folder:**
1. The global Webflow MCP Field Guide: https://raw.githubusercontent.com/stephenjhebert-lgtm/web-build-standards/main/standards/webflow/mcp-field-guide.md
2. This file
3. `./source-of-truth.md` (site state)

---

## Site identity

- **Brand:** [brand name]
- **Webflow site ID:** [to fill in]
- **Live URL:** [to fill in]
- **Staging URL:** [site-id.webflow.io]
- **Custom domain:** [to fill in]

## Brand tokens (overrides to any generic design guidance)

- **Primary color:** [hex]
- **Secondary color:** [hex]
- **Accent color:** [hex]
- **Heading font:** [font + fallbacks]
- **Body font:** [font + fallbacks]

## Site-specific quirks

Document anything here that differs from the global standards or that the field guide doesn't cover. Examples: "the hero uses a helper div pattern because style_tool rejected the gradient stack"; "CMS collection X has schema-level requirement Y"; "custom code block in Head has 40 lines — see `webflow_head_paste.html`".

## Known inline-style workarounds

Any inline `style=""` attribute applied to work around MCP issues. Record element ID, the property, and why.

| Element | Property | Value | Reason | Date |
|---|---|---|---|---|

## Human-in-the-loop queue

Anything that requires Steve to do something manually (asset drops, Webflow Designer edits, plan changes). Keep this current.

- [ ]

## Links

- Notion project page: [url]
- Figma / design source: [url]
- Source-of-truth file: `./source-of-truth.md`
