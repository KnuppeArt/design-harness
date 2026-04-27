# /decompose

Analyze an existing UI and generate components.json entries from it (brownfield mode).

## Agent
@ui-architect

## Skills loaded
1. `project-context-resolver`
2. `design-system-reader`
3. `brownfield-decomposer`

## Usage

```
/decompose                      ← from screenshots (user provides images)
/decompose --from-code          ← from existing component files
/decompose --from-pen           ← from existing .pen file
```

## When to use
- The project has an existing UI (designed in Figma, built in code, or sketched)
- You want to retrofit it into the Design Harness SSOT format
- You're migrating an existing design system to this pipeline

## Process

1. Determine input mode from argument or by asking user
2. Run `brownfield-decomposer` with the appropriate input
3. Present the decomposition plan (component inventory, classification, atomic hierarchy)
4. Wait for user approval of the decomposition plan
5. Generate components.json entries for approved decomposition
6. Flag token gaps (visual values with no DESIGN.md match)
7. Validate components.json against schema

## Output
Updated `components.json` + decomposition gap report.

## Notes
- If DESIGN.md doesn't exist, run `/gen-design-system` first, then `/decompose`
- If only some screens are provided, note which screens are covered and which are not
- Decomposed components can be revised iteratively — this is not a one-shot operation
