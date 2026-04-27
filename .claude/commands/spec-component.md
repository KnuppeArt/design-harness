# /spec-component

Translate approved Pencil component designs into components.json entries.

## Agent
@ui-architect

## Skills loaded
1. `design-system-reader` — token map for annotation
2. `component-specifier` — read Pencil, build spec entries, validate

## Usage

```
/spec-component              ← batch: spec all approved components in current layer
/spec-component Button       ← single: spec one specific component
```

## Requires
- Human approval of the current layer's Pencil designs (from `/design-component`)
- Pencil MCP connected (to read .pen file)
- components.json exists in project root
- schemas/components-registry.v1.schema.json exists

## Process

### Batch mode
1. Read the current layer's .pen file via Pencil MCP
2. For each component frame, run `component-specifier`:
   - Map layers to anatomy parts with DESIGN.md token assignments
   - Extract variants and states from frame naming convention
   - Document behavior (gestures, animations, accessibility)
   - Extract constraints (min/max dimensions)
3. Merge all new entries into components.json
4. Validate: `npx ajv validate -s schemas/components-registry.v1.schema.json -d components.json`
5. Fix validation errors
6. Report: list all specced components with variant/state counts
7. Mark layer as complete in build plan

### Single mode
Same process for one named component. Used for corrections after batch.

## Output
Updated `components.json` (validated).

## What to do with token gaps
If a visual property in Pencil has no matching DESIGN.md token:
1. Note it as a "design gap" in the response
2. Ask: "Add to DESIGN.md as a new token, or use inline value?"
3. Do not proceed until the user decides
4. If adding to DESIGN.md: make the change and re-lint DESIGN.md

## Validation requirement
components.json must pass schema validation before this command is considered complete. Do not report success if validation fails.
