# /wireframe

Generate a skeleton layout mapping components to views — the structural blueprint before visual design.

## Agent
@ui-composer

## Skills loaded
1. `project-context-resolver`
2. `design-system-reader`
3. `wireframer`

## Usage

```
/wireframe                   ← from project docs (default)
/wireframe --from-image      ← interpret from user-provided wireframe image
/wireframe --from-pen        ← import from existing .pen file
```

## Requires
- components.json fully populated (all layers complete + approved)
- PRD or UX spec available (for default mode)
- Pencil MCP connected

## Process

Run `wireframer` with the appropriate input mode. Always produces:
1. `wireframe.json` — structured skeleton (machine-readable)
2. `wireframe.pen` — visual skeleton in Pencil (human-reviewable)

Present both outputs. Ask: "Does this capture all views and their component structure correctly?"

## Approval gate
User must explicitly approve the wireframe before `/design-view` can proceed.

Changes requested → update wireframe.json + wireframe.pen → present again.

## What wireframe approval means
The user is approving:
- ✓ This is the complete list of views
- ✓ These components are in the right views
- ✓ The rough order of sections is correct
- ✓ Navigation between views is correct

The user is NOT approving:
- Visual design (colors, typography)
- Exact spacing and sizes
- Final layout details

Those come in `/design-view`.
