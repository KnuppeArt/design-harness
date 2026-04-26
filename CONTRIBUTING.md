# Contributing to Design Harness

Design Harness is an open system — designed to be extended with new skills, commands, schemas, and agent personas. Here's how.

---

## What you can contribute

### New skills
Skills are modular knowledge files loaded by agents on demand. Good candidates:
- Platform-specific design conventions (iOS HIG, Material Design 3, web)
- Domain-specific component patterns (e-commerce, dashboards, data-heavy apps)
- Alternative design tools (Penpot, Sketch, Adobe XD)
- Quality evaluation skills (accessibility, performance, motion)

### New commands
Commands trigger skill-based workflows. Good candidates:
- `/audit` — evaluate existing SSOT against a new standard
- `/migrate` — convert specs from one format to another
- `/diff` — compare two versions of components.json
- `/export` — export VIEWS.md to specific framework format

### Schema improvements
- New fields in `components-registry.v1.schema.json`
- New schema types (animations, interactions, accessibility)
- Validation rule improvements

### Agent improvements
- Better context loading strategies
- Improved prompt patterns for specific use cases
- New agent personas for specialized domains

---

## Skill structure

Every skill lives in its own directory with a single `SKILL.md` file:

```
.claude/skills/your-skill-name/
  SKILL.md
```

`SKILL.md` structure:

```markdown
# Skill: Your Skill Name

## Purpose
One sentence: what this skill enables an agent to do.

## When to load
Specific trigger: which command or situation activates this skill.

## Context requirements
What must exist before this skill can be used:
- Required files: DESIGN.md, components.json, etc.
- Required state: what must be completed upstream.

## Process
Step-by-step: what the agent does when this skill is active.

## Output
What this skill produces, including file format and location.

## Validation
How to verify the output is correct.
```

---

## Command structure

Commands live in `.claude/commands/`:

```markdown
# Command: /your-command

## Agent
@ui-architect | @ui-composer | both

## Skills loaded
- skill-name-1
- skill-name-2

## Behavior
What happens when this command runs.

## Input
- No arg: default behavior
- With arg: specific behavior

## Output
What files are produced or updated.

## Gates
Any approval gates before advancing.
```

---

## Schema contributions

Schemas follow JSON Schema draft-07. When adding fields:
1. Add with `"required": false` and a default value
2. Document in a comment block above the field
3. Maintain backward compatibility — existing `components.json` files must remain valid
4. Add a test case in `schemas/tests/`

---

## Pull request guidelines

- One PR per skill, command, or feature
- Include a `DESIGN_DECISIONS.md` note explaining architectural choices
- Test with at least one real project before submitting
- Skills must be tested with Claude Code (not just reviewed)

---

## Not in scope

- Generating code (Design Harness produces specs, not implementations)
- Framework-specific output (SSOT is stack-agnostic by design)
- Hardcoding project-specific logic into skills

---

## License

Contributions are licensed under Apache 2.0.

---

*Design Harness — Contributing Guide*
