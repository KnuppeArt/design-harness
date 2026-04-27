# /init-design

Bootstrap a new project with the Design Harness pipeline structure.

## Agent
Any (runs at user level, project-agnostic)

## Usage

```
/init-design
```

No arguments. Project name is inferred from the current directory name.

## What it creates

```
[project-root]/
  .claude/
    agents/
      ui-architect.md     ← copy from design-harness kit
      ui-composer.md      ← copy from design-harness kit
    skills/
      project-context-resolver/SKILL.md
      design-system-reader/SKILL.md
      design-system-generator/SKILL.md
      build-planner/SKILL.md
      component-specifier/SKILL.md
      brownfield-decomposer/SKILL.md
      wireframer/SKILL.md
      view-composer/SKILL.md
      view-specifier/SKILL.md
      design-qa/SKILL.md
    commands/
      gen-design-system.md
      plan-components.md
      design-component.md
      spec-component.md
      decompose.md
      wireframe.md
      design-view.md
      spec-view.md
  schemas/
    components-registry.v1.schema.json
    views-registry.v1.schema.json
  docs/                   ← empty, ready for brief.md, prd.md, etc.
  DESIGN.md               ← empty template (or skip if already exists)
  components.json         ← empty registry (schema + empty components object)
  views.json              ← empty registry (schema + empty views object)
  CLAUDE.md               ← project-level Design Harness rules
  .gitignore              ← ignores Pencil cache files
```

## Process

1. Detect project name from current directory (do NOT ask the user)
2. Check what already exists — do not overwrite:
   - If DESIGN.md exists with content → skip, note it
   - If components.json exists with content → skip, note it
   - If views.json exists with content → skip, note it
   - If .claude/ already has agents/skills/commands → ask before overwriting
3. Create missing directories and files
4. Set project name in components.json and views.json from directory name
5. Platform is always "cross-platform" (pipeline is stack-agnostic — do NOT ask about platform)
6. Report what was created vs. skipped

## Output report format

```
✓ Design Harness initialized for: [ProjectName]

Created:
  ✓ .claude/agents/ (2 agents)
  ✓ .claude/skills/ (10 skills)
  ✓ .claude/commands/ (8 commands)
  ✓ schemas/ (2 schemas)
  ✓ components.json (empty registry)
  ✓ views.json (empty registry)
  ✓ CLAUDE.md
  ✓ .gitignore

Skipped (already exist):
  - DESIGN.md (has content — run /gen-design-system to regenerate)

Next steps:
  1. Add your project brief to docs/brief.md
  2. Run @ui-architect /gen-design-system (if no DESIGN.md)
  3. Run @ui-architect /plan-components (requires brief + DESIGN.md)
```

## Notes
- If the kit is installed at `~/.claude/design-harness/`, copy from there
- If not, create files from embedded templates
- The `.gitignore` should include Pencil cache entries: `.pencil-cache/`, `*.pen.bak`
