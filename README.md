# Code to Canvas Skill

A Claude Code skill that orchestrates a full design-to-Figma workflow in one command.

## What it does

When you invoke `/code-canvas`, it:

1. Builds your interface as a Next.js component or page
2. Starts the local dev server
3. Captures the live UI into Figma via Code to Canvas
4. Structures the Figma file automatically:
   - Detects and applies the design system library
   - Replaces raw colors and fonts with design tokens
   - Applies auto-layout to every frame (children set to Fill/Hug)
   - Creates components with context-aware variants on a "Components" page
   - Renames every layer to a unique, semantic name
   - Sets correct breakpoint widths (mobile or desktop) based on context
5. Hands off the Figma file URL for you to continue in the browser

## Install

```bash
npx skills add nuckecy/code-to-canvas-skill@code-canvas
```

## Usage

```
/code-canvas [url or description]
```

Or just say **"build me a [thing]"** and Claude will offer to invoke it automatically.

If no URL is provided, the skill will ask for one. It will also ask any clarifying questions (platform, Figma file) before starting.

## Requirements

- [Claude Code](https://claude.ai/code)
- Figma MCP plugin configured in Claude Code
- GitHub CLI (`gh`) authenticated (optional, for Figma file creation)

## How it works

The skill chains three Claude Code capabilities:

1. **Next.js build** - Scaffolds the interface with TypeScript and Tailwind CSS
2. **Code to Canvas** - Captures the live UI from localhost and sends it to Figma as editable layers
3. **Figma post-processing** - Structures the file to production standards using the Figma Plugin API

## Skill file location after install

```
~/.claude/skills/code-canvas/SKILL.md
```
