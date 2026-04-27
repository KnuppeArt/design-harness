# Skill: wireframer

## Purpose
Map approved components to views as a skeleton layout — answering "what goes where" before designing visual details.

## When to load
Only when `/wireframe` is invoked.

## Input modes
1. **From docs (default):** Reads PRD/UX spec + components.json → generates skeleton
2. **From image (`--from-image`):** User provides wireframe sketch/screenshot → Claude interprets via vision → produces structured output
3. **From pen (`--from-pen`):** User provides existing .pen file → reads via Pencil MCP → produces structured output

All three modes produce the same two outputs: `wireframe.json` + `wireframe.pen`.

## Prerequisites
- `project-context-resolver` run (knows where PRD/UX spec lives)
- `design-system-reader` loaded
- components.json exists and is not empty (all atomic layers approved)

## Process (from-docs mode)

### 1. Extract view list from project docs
From PRD/UX spec, list every screen, modal, and sheet. For each, note:
- Navigation entry point (how users get here)
- Primary purpose (one sentence)
- Key features / content required

### 2. For each view, identify components
Cross-reference with components.json. For each view, list:
- Which components appear?
- What is the rough vertical order?
- Which components are conditional (show only in certain states)?
- Which components are fixed/sticky vs. in flow?

Do NOT yet define exact positions, sizes, or spacing — that's /design-view.

### 3. Build wireframe.json
```json
{
  "version": "0.1.0",
  "project": "ProjectName",
  "views": {
    "HomeScreen": {
      "title": "Home",
      "entry_points": ["tab-bar-home"],
      "sections_sketch": [
        { "component": "NavBar", "placement": "fixed-top", "notes": "sticky on scroll" },
        { "component": "SkyBackground", "placement": "full-bleed", "notes": "dynamic, behind content" },
        { "component": "HeroDateWidget", "placement": "flow", "notes": "centered, primary focus" },
        { "component": "FeastBanner", "placement": "flow", "notes": "conditional: only when feast active" },
        { "component": "TabBar", "placement": "fixed-bottom", "notes": "" }
      ]
    }
  }
}
```

### 4. Build wireframe.pen
Use Pencil MCP to create a visual skeleton:
- One frame per view
- Components represented as labeled rectangles with component names
- No colors — grayscale only
- No typography details — placeholder text only
- Annotations for conditional elements and placement types
- Navigation arrows between frames

### 5. Present for approval
Show wireframe.json + Pencil link. Ask: "Does this capture all views and their component structure correctly?"

Human MUST approve before /design-view proceeds.

## Process (from-image mode)
1. User provides image
2. Analyze via vision: identify sections, UI elements, rough layout
3. Map identified elements to components.json (by name matching + visual similarity)
4. Flag any visual elements with no match in components.json
5. Produce wireframe.json + wireframe.pen as above

## Process (from-pen mode)
1. Read existing .pen via Pencil MCP
2. Map frame names and layer names to components.json
3. Extract rough layout structure
4. Produce wireframe.json from existing .pen

## What a wireframe is NOT
- Not a pixel-perfect mockup
- Not a final layout spec
- Not a place for color, typography, or spacing decisions
- Not an approval of visual design — only approval of structure
