# Skill: brownfield-decomposer

## Purpose
Analyze an existing UI (from code, screenshots, or an existing .pen file) and decompose it into an atomic component hierarchy, producing components.json entries that match the Design Harness SSOT format.

## When to load
Only when `/decompose` is invoked.

## Input modes
1. **From image (default):** User provides screenshots → Claude uses vision to analyze UI
2. **From code:** User provides component files → Claude reads implementation and extracts design spec
3. **From .pen:** User provides an existing Pencil file → read via Pencil MCP

## Process

### 1. Inventory the UI
For each screen/view provided:
- List every distinct UI element visible
- Note variants (e.g., a button that appears in 3 different colors is 1 component with 3 variants)
- Note states (e.g., a toggle in on/off is 1 component with 2 states)

### 2. Match to DESIGN.md tokens (if available)
If DESIGN.md exists:
- Map visual colors to the nearest DESIGN.md token
- Map visual typography to the nearest typography token
- Flag any values that don't match existing tokens (token gaps)

If no DESIGN.md:
- Extract raw values and note them — they'll become the basis for generating DESIGN.md

### 3. Classify by atomic level
Apply the same criteria as `build-planner`:
- **Atom:** Can't be decomposed further
- **Molecule:** Composed of 2+ atoms
- **Organism:** Complex section combining molecules/atoms

Flag ambiguous cases and ask user before finalizing.

### 4. Build component specs
For each component, produce the same spec structure as `component-specifier`:
- anatomy (visual parts)
- variants (observed variants)
- states (observed states)
- tokens (mapped to DESIGN.md or raw values with gap flags)
- behavior (interactions observed or inferred from UX context)
- constraints (dimensions from designs or code)

### 5. Flag gaps
Report what's missing or ambiguous:
```
# Decomposition Gaps
- Button: pressed state not visible in screenshots — inferring from design patterns
- DateWidget: custom blur effect not in DESIGN.md — needs token DA-XX
- NavBar: desktop behavior unknown — only mobile screenshots provided
```

### 6. Write to components.json
Same validation step as `component-specifier`.

## Output
Updated `components.json` + gap report in response.

## When to ask vs. infer
- **Ask:** Missing state that has meaningful behavior (e.g., error state for an input)
- **Ask:** Ambiguous classification (atom vs. molecule)
- **Infer:** Standard platform conventions (e.g., iOS button has pressed state with opacity)
- **Infer:** Disabled state from default state (opacity 0.4, no interactions) unless the design clearly shows something different
