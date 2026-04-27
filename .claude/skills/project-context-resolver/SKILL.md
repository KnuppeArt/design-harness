# Skill: project-context-resolver

## Purpose
Detect which project documents exist and resolve where each type of information lives — so agents never hardcode filenames or assume document structure.

## When to load
First action when any Design Harness agent activates. Always load before reading any project docs.

## The problem this solves
Projects have different documentation depth:
- Minimal: `brief.md` only
- Standard: `brief.md` + `prd.md`
- Full: `brief.md` + `prd.md` + `ux-spec.md` + `trd.md` + `domain-rules.md` + `content-spec.md`

An agent that hardcodes filenames breaks on any variation. This skill makes agents adaptive.

## Resolution map

For each information type, check sources in priority order and use the first that has it:

| Information type | Check in order |
|-----------------|----------------|
| Brand, audience, mood, vision | `prd.md` → `brief.md` |
| Screens / views / features | `ux-spec.md` → `prd.md` → `brief.md` |
| User flows / interactions / states | `ux-spec.md` → `prd.md` |
| Domain rules / business logic | `domain-rules.md` → `prd.md` → `brief.md` |
| Tech stack / platform constraints | `trd.md` → `prd.md` → `brief.md` |
| Content / copy | `content-spec.md` → `prd.md` |
| Acceptance criteria | `prd.md` → `brief.md` |

## Process

1. Scan for project docs in this order of locations:
   - `docs/` directory (preferred structure)
   - Project root
   - Any `.md` file with a recognizable name pattern

2. Build an internal resolution map:
   ```
   AVAILABLE DOCS:
   - brief: docs/brief.md ✓
   - prd: docs/prd.md ✓
   - ux-spec: NOT FOUND → fallback to prd/brief
   - domain-rules: docs/domain-rules.md ✓
   - trd: NOT FOUND → fallback to prd/brief
   ```

3. When any subsequent skill or command needs information, consult this map to know where to look.

4. If critical information is missing from all available sources, ask the user — do not guess.

## What "critical" means by command

| Command | Critical missing info = STOP |
|---------|------------------------------|
| `/gen-design-system` | Brand colors, typography intent, aesthetic direction |
| `/plan-components` | List of screens/views, key features per screen |
| `/design-component` | Component purpose, key states, usage context |
| `/wireframe` | List of views, navigation structure, key user flows |

## Output
An internal resolution map used by subsequent skills. Not written to disk.
