# Skill: component-specifier

## Purpose
Translate approved Pencil designs (.pen file) into structured components.json entries that validate against `components-registry.v1.schema.json`.

## When to load
Only when `/spec-component` is invoked.

## Prerequisites
- `design-system-reader` loaded (token map available)
- Pencil MCP available (to read .pen file)
- Approved designs exist for the current atomic layer
- components.json exists in project root (even if empty registry)

## Process

### 1. Read the .pen file
Use Pencil MCP to read the current layer's Pencil file (atoms.pen, molecules.pen, or organisms.pen). For each component frame:
- Read all layers and their properties
- Note every visual state (frames named e.g. "Button/primary/default", "Button/primary/pressed")
- Note every variant frame

### 2. For each component, build the spec

**level:** Derive from build plan (atom/molecule/organism)

**description:** One sentence from the build plan description

**uses:** For molecules/organisms only — list which components from components.json this one is built from (check Pencil layers for component instances)

**anatomy:** Map each named layer group to an anatomy part:
- Text layer → `"type": "text"`, assign the closest DESIGN.md typography token
- Color fill → assign the closest DESIGN.md color token
- Container/background → `"type": "shape"` or `"type": "container"`
- Image placeholder → `"type": "image"`
- Blur layer → `"type": "blur-layer"`
- Nested component instance → `"type": "component"`, add `"component_ref": "ComponentName"`
- Mark optional layers (present in some states but not default) as `"required": false`

**variants:** One entry per variant frame. Name matches the frame naming convention (e.g., `primary`, `secondary`, `ghost`). Note token overrides vs. base.

**states:** One entry per state frame. Minimum: `default`. Add `hover`, `pressed`, `focused`, `disabled`, `loading`, `error` as present in designs.

**tokens:** Add Tier 3 overrides only if the component uses values that deviate from Tier 2 semantic tokens.

**behavior:** Document gestures (tap, long-press, swipe, etc.), animations (note duration and easing from design), and accessibility requirements (role, label pattern, focusable).

**constraints:** Extract from Pencil frame dimensions: min/max width/height, aspect ratio if constrained.

### 3. Write to components.json
Merge new entries into the existing components.json. Never overwrite existing entries — append only during a session. If a component already exists, update it only if the design has changed.

### 4. Validate
```bash
npx ajv validate -s schemas/components-registry.v1.schema.json -d components.json
```
Expected: `components.json valid`

Fix any validation errors before reporting completion.

### 5. Report
After writing, present a summary:
```
Specced N components:
  ✓ Button (atom) — 3 variants, 4 states
  ✓ Icon (atom) — 1 variant, 2 states
  ✓ Badge (atom) — 2 variants, 3 states

components.json validated ✓
```

## Token assignment rules

When assigning tokens to anatomy parts, use this decision tree:
1. Does DESIGN.md have a Tier 3 token for this component + part? → Use it
2. Is there a Tier 2 semantic token that clearly maps to this role? → Use it
3. Is the value a raw primitive (Tier 1)? → Find the semantic token that references it, use the semantic token
4. No token match? → Note it as a token gap and ask user to add it to DESIGN.md before proceeding

## What to do with undocumented design decisions

If the Pencil design includes visual choices not documented in DESIGN.md (e.g., a specific blur value, a non-token border radius), note them in a `# Design Gaps` section in your response and ask the user whether to:
a) Add them as new tokens to DESIGN.md, or
b) Document them as inline values in the component spec (with a comment explaining they're not tokenized)
