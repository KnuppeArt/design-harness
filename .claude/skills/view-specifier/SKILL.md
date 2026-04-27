# Skill: view-specifier

## Purpose
Translate approved Pencil view designs into a complete, validated views.json — so complete that a coding agent implements the UI without making a single visual decision.

## When to load
Only when `/spec-view` is invoked.

## Prerequisites
- Approved view designs in Pencil (all views)
- components.json fully populated
- `design-system-reader` loaded
- views-registry.v1.schema.json available in schemas/

## The completeness standard

A views.json entry passes the completeness test when a junior developer can read it and implement the screen without asking any questions. This means:

**Every section must have:**
- `component` — exact name from components.json
- `variant` — which variant (if component has variants)
- `state` — initial render state
- `position.placement` — flow, fixed, sticky, absolute, or floating
- `position.alignment` — left, center, right, full-width, full-bleed
- `dimensions.width` — explicit value or "full"
- `dimensions.height` — explicit value or "auto" (only if genuinely content-driven)
- `spacing.margin_top` — distance from previous section

**Every view must have:**
- `behavior.scroll` — none, vertical, horizontal, both
- `behavior.background` — token reference or special value
- `behavior.safe_area` — top and bottom booleans
- `behavior.status_bar` — light, dark, or auto
- `grid` — columns, gutter, margin, max_width

## Process

### 1. Read each approved Pencil frame
Use Pencil MCP to read each view frame. Extract:
- Frame dimensions (device canvas size)
- Each layer with its position (x, y), size (w, h), and name
- Layer group hierarchy (corresponds to sections)
- Fixed/pinned layers (these are fixed/sticky placements)

### 2. Build each section spec
For each layer group in the Pencil frame:

**component:** Match layer group name to components.json key. If no match, STOP and flag it.

**position.placement:** Determine from Pencil constraints:
- Pinned to top/bottom = `fixed` or `sticky`
- Normal flow = `flow`
- Positioned absolutely = `absolute`
- Overlapping content, not in flow = `floating`

**dimensions:** Extract from Pencil:
- Width as percentage of frame or "full" if 100%
- Height as explicit px value or "auto" if content-driven

**spacing.margin_top:** Calculate from y-position of current section minus (y + height) of previous section. Convert to spacing token if it matches one; otherwise use explicit px value.

### 3. Resolve breakpoints
For each breakpoint frame in Pencil (if present), produce the `breakpoints` overrides. Only include sections that change — not the full view repeated.

### 4. Resolve interactions
From Pencil interaction annotations (or from components.json behavior), produce the `interactions` array for each section. Every tap/swipe maps to a named action.

### 5. Write views.json
Merge all view specs into views.json. Validate:
```bash
npx ajv validate -s schemas/views-registry.v1.schema.json -d views.json
```
Expected: `views.json valid`

### 6. Cross-validate against components.json
Every `component` value in views.json must exist as a key in components.json. Every `variant` value must exist in that component's `variants`. Report any mismatches.

### 7. Report
```
Specced N views:
  ✓ HomeScreen — 6 sections, 1 conditional
  ✓ CircularCalendarView — 4 sections
  ✓ MonthExplorer — 5 sections, 1 sheet

views.json validated ✓
Cross-check against components.json: ✓ all references valid
```

## What "auto" height means (use sparingly)
Use `"height": "auto"` ONLY when the section's height is genuinely determined by dynamic content at runtime (e.g., a list that grows with data). For everything else, use explicit values — even approximate ones (e.g., `"height": "~180px"`) are more useful to a coding agent than "auto".
