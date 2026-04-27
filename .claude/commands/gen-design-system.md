# /gen-design-system

Generate a complete DESIGN.md for this project from the available brief and project documents.

## Agent
@ui-architect

## Skills loaded
1. `project-context-resolver` — find and read project docs
2. `design-system-reader` — understand target output format
3. `design-system-generator` — generate and validate DESIGN.md

## Process

1. Run `project-context-resolver` to locate project docs
2. Extract design direction from brief: aesthetic intent, color direction, typography, platform, any explicit brand colors
3. If critical design direction info is missing, ask before proceeding
4. Generate DESIGN.md using `design-system-generator`:
   - Build Tier 1 primitives (full palette)
   - Build Tier 2 semantic tokens (all UI roles)
   - Build Tier 3 component tokens (if applicable)
   - Add animation, elevation, blur tokens if the app needs them
5. Run: `npx @google/design.md lint DESIGN.md`
6. Fix any lint errors
7. Present DESIGN.md to user with a summary of key decisions made

## Output
`DESIGN.md` written to project root.

## Lint requirement
Must pass `npx @google/design.md lint DESIGN.md` with 0 errors before presenting.

## Notes
- If DESIGN.md already exists, ask: "DESIGN.md already exists. Regenerate from scratch, or update it?"
- Regenerating overwrites the existing file — confirm with user
- The existing DESIGN.md for Kairos is already validated; do not regenerate unless asked
