# /spec-view

Translate approved Pencil view designs into a complete, validated views.json.

## Agent
@ui-composer

## Skills loaded
1. `design-system-reader`
2. `view-specifier`

## Usage

```
/spec-view              ← all approved views
/spec-view HomeScreen   ← one specific view
```

## Requires
- All view designs approved in Pencil
- components.json fully populated and validated
- schemas/views-registry.v1.schema.json exists
- Pencil MCP connected

## Process

Run `view-specifier` for each view:
1. Read each approved Pencil frame
2. Extract sections with explicit positions, dimensions, spacing
3. Resolve interactions from annotations
4. Build views.json entries
5. Validate: `npx ajv validate -s schemas/views-registry.v1.schema.json -d views.json`
6. Cross-validate: every component reference in views.json exists in components.json
7. Report completeness

## Completeness check
Before reporting success, verify every view passes this checklist:
- [ ] All sections have explicit width + height (no undefined values)
- [ ] All sections have margin_top (never missing)
- [ ] All fixed/sticky elements have explicit anchor points
- [ ] All conditional sections have `show_when` documented
- [ ] All interactions are mapped to named actions
- [ ] behavior (scroll, background, safe_area, status_bar) is defined
- [ ] grid (columns, gutter, margin, max_width) is defined
- [ ] Breakpoint overrides exist for any section that changes at tablet/desktop

## Output
`views.json` — validated, cross-referenced. The final SSOT artifact.

## This is the last step of the Design Harness pipeline
After `/spec-view` completes, the SSOT is:
- `DESIGN.md` — tokens
- `components.json` — component specs
- `views.json` — view layouts

Hand this to any coding agent for implementation.
