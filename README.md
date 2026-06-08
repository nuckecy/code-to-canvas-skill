# Code to Canvas Skill

A Claude Code skill that orchestrates a full design-to-Figma workflow: builds a Next.js interface, captures the live UI via Code to Canvas, then performs structured Figma post-processing to production-quality standards.

## What it does

When you invoke `/code-canvas`, it:

1. Builds your interface as a Next.js component or page
2. Starts the local dev server
3. Captures the live UI into Figma via Code to Canvas
4. Structures the Figma file to production-quality standards automatically — tokens, components, layout, and accessibility included
5. Runs a standards audit loop until the design scores 100% before handoff
6. Hands off the Figma file URL for you to continue in the browser

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

## Standards enforced

- Auto-layout on every frame (Fill/Hug children)
- Components with exhaustive variants on a dedicated "Components" page: every component is checked against a 10-category variant trigger checklist (interactive states, loading/async, validation, empty/skeleton, size, style/type, status, content slots, selection/toggle, expansion) and all applicable variants are created
- Unique, semantic layer names (zero generic names)
- All values reference design tokens (no hardcoded colours or typography)
- Accessibility: AA required, AAA where feasible; non-text UI 3:1+; no colour-only meaning; body text 14px+

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

## Author

**Otobong Okoko** - otobongsok@gmail.com

## License

MIT License. See [LICENSE](LICENSE) for details.
