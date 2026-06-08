---
name: code-canvas
description: Orchestrates a full design-to-Figma workflow: builds a Next.js interface, captures the live UI via Code to Canvas, then performs structured Figma post-processing (design system adoption, tokens, auto-layout, components with variants, semantic naming, correct breakpoints). Triggers automatically when the user says "build me a [thing]" and offers to invoke the workflow. Also invoked directly via /code-canvas [url or description].
---

# Code to Canvas Workflow

Builds a Next.js interface, captures the live UI into Figma via Code to Canvas, then structures the Figma file to production-quality standards.

## Trigger: "Build me a [thing]"

When the user says something like "build me a dashboard", "build me a landing page", "build me a modal" — before starting, ask:

> "Would you like to invoke /code-canvas with this? It will build the Next.js interface, capture it live into Figma, and structure the design automatically."

If yes, proceed with the full workflow below. If no, build the Next.js interface only.

## Invocation

```
/code-canvas [optional: URL or description]
```

- If a URL is provided (localhost, staging, or production), use it as the capture target.
- If no URL is provided, ask: "What URL or interface should I capture? Or describe what you want built."
- After collecting the URL or description, ask any clarifying questions before proceeding (see Follow-up Questions below).

## Follow-up Questions

Before starting, ask only what is not already clear from context:

1. **Target** (if not provided): "What are we building or capturing?"
2. **Platform**: "Is this mobile, desktop, or both?" (infer from context if obvious — a "landing page" is desktop, a "profile screen" is mobile)
3. **Figma file**: "Do you have an existing Figma file to use, or should I create a new one?"
4. **Design system**: "Does the Figma file already have a design system library attached?" (if unknown, the skill will detect it automatically)

Do not ask questions that are already answerable from the task description.

## Workflow

### Step 1: Build the Next.js Interface

- Scaffold or generate the interface as a Next.js page or component.
- Use TypeScript by default.
- Use Tailwind CSS unless the project uses another styling system.
- Ensure the component is responsive and matches the described platform (mobile or desktop).
- Do not add features beyond what was described.

### Step 2: Start the Dev Server

- Run the local dev server (e.g. `npm run dev` or `next dev`).
- Confirm the server is running and the target URL is accessible.
- Default to `http://localhost:3000` unless otherwise specified.

### Step 3: Code to Canvas Capture

- Load the `figma-generate-design` skill and `figma-use` skill before proceeding.
- Use the `generate_figma_design` tool to capture the live UI from the dev server URL.
- Send it to the specified Figma file (or create a new one if none exists).
- Wait for the capture to complete before proceeding to post-processing.

### Step 4: Figma Post-Processing

Perform all of the following inside Figma after the design lands. Use the `use_figma` tool (with `figma-use` skill loaded) for all write operations.

#### 4a. Detect and Apply Design System

- Search the file for any attached or published design system libraries using `get_libraries`.
- If a library is found, apply its components, color styles, and text styles to the captured design.
- Swap raw elements for library components where a match exists.
- If no library is found, note this to the user and proceed with structuring.

#### 4b. Replace Raw Values with Tokens

- Replace all hardcoded hex color fills with the corresponding library color variables/tokens.
- Replace all hardcoded font sizes, weights, and families with library text style tokens.
- No raw values should remain after this step. Every color and text property must reference a token.

#### 4c. Apply Auto-Layout to Every Frame

- Every frame in the design must use auto-layout. No freeform or absolute frames.
- Set direction (horizontal or vertical) based on the visual layout of the frame's children.
- For every child element inside an auto-layout frame:
  - Set horizontal sizing to **Fill** if it should stretch across the frame width.
  - Set vertical sizing to **Hug** if it should wrap its content height.
  - Use **Fixed** only when a dimension must not change (e.g. an icon or avatar with a defined size).
- Nest frames as needed to achieve the correct layout structure.

#### 4d. Componentize Reusable Patterns

- Identify any UI pattern that appears more than once or represents a discrete, reusable element (buttons, cards, input fields, badges, navigation items, etc.).
- Create a Figma component for each identified pattern.
- **Variants**: If the task or design context implies multiple states (e.g. a button implies default, hover, disabled; a card implies default and selected), create a component set with those variants. Only include variants that are contextually justified — do not fabricate states.
- Token-bind all fills and text styles within components. No hardcoded values inside components.
- Place all components on a dedicated page named **"Components"**.

#### 4e. Semantic Layer Naming

- Rename every layer to a unique, meaningful name based on its purpose and the task context.
- No generic names: no "Frame 1", "Rectangle 2", "Group 3", "Container", "Div".
- Use slash notation for hierarchy where appropriate (e.g. `card/product`, `nav/top-bar`, `button/primary`), but adapt the naming convention to what makes the most sense for the specific task.
- Names should be readable and self-documenting to any designer opening the file.

#### 4f. Top-Level Frame Sizing

- Infer the target breakpoint from the task context:
  - Mobile: 375px wide (iPhone default) or 390px for newer iPhones.
  - Tablet: 768px wide.
  - Desktop: 1440px wide.
  - If both mobile and desktop are needed, create separate frames for each.
- Set top-level frame width to the appropriate fixed breakpoint value.
- Height can be set to hug content unless a fixed viewport height is required.

#### 4g. Accessibility Audit and Fixes

- **Text contrast:** Every text layer must meet AA as a hard floor (4.5:1 for normal text, 3:1 for large text where large = 24px+ or 19px+ bold). Aim for AAA (7:1 normal, 4.5:1 large) on body and critical text. AA is acceptable only on large display, decorative, or brand-locked elements where AAA is impractical.
- **Non-text/UI contrast:** Icons, borders, focus rings, and status indicators must meet a minimum contrast ratio of 3:1 against their background.
- **No colour-only meaning:** Wherever colour is used to convey meaning (e.g. error states, status indicators, links), pair it with a text label, icon, or shape. Never rely on colour alone.
- **Minimum text size and spacing:** Body text must be at least 14px with adequate line-height. Interactive targets must be comfortably sized for touch and click.
- **Verify, do not eyeball:** Compute actual contrast ratios for every colour pairing before completing this step. Do not estimate or assume contrast passes.
- **Exception (production mirror):** If the design is a pixel-faithful mirror of an existing/production UI, preserve the real values even if they fail accessibility checks. Flag every failure explicitly to the user rather than silently correcting the mirror.

### Step 5: Standards Audit Loop

After all post-processing steps (4a through 4g) complete, run a full audit against every standard before handoff. Score each criterion as pass (1) or fail (0), then express the result as a percentage of the total criteria count. Repeat the loop until the score is 100%. There is no maximum iteration cap: keep looping until 100% is reached. If new standards are added in future, they are automatically included in the audit and the percentage recalculates accordingly.

#### Audit Scorecard (score = passing criteria / total criteria x 100)

| # | Criterion | Pass condition |
|---|-----------|----------------|
| 1 | Auto-layout | Every frame uses auto-layout; all children are Fill or Hug (never fixed or absolute, except flagged production mirrors) |
| 2 | Components and variants | All repeated/stateful elements are components with context-accurate variants, masters on the "Components" page, zero instances of raw repeated elements |
| 3 | Semantic layer names | Zero generic names (Frame N, Rectangle N, Group N, Container, Div); every layer name is unique and describes what it is |
| 4 | Token coverage | Zero hardcoded hex colours, font sizes, weights, or families; all values reference library tokens/variables |
| 5 | Accessibility | All text contrast AA+, body/critical text targeting AAA, non-text UI 3:1+, no colour-only meaning, body text 14px+ with adequate spacing |

#### Loop behaviour

1. After each audit, report the current score as a percentage (e.g. "Audit: 60% — failing criteria: 3, 5").
2. For each failing criterion, re-run the corresponding post-processing sub-step(s) to fix the issues.
3. Re-audit immediately after fixes are applied.
4. Repeat until the score is 100%.
5. Only proceed to Step 6 (Handoff) once the score is 100%.

### Step 6: Handoff

- Once the audit score is 5/5, surface the Figma file URL to the user.
- Tell the user: "Your design is ready in Figma (audit score 100%). Open the link to inspect and continue refining using Code to Canvas controls."
- Provide the direct link to the Figma file.

## Rules

- Always load `figma-use` before any `use_figma` call.
- Always load `figma-generate-design` before any `generate_figma_design` call.
- Steps 1-2 (Next.js build and dev server) run first, then Step 3 (capture), then Steps 4a-4g (post-processing) run in sequence inside Figma, then Step 5 (audit loop), then Step 6 (handoff).
- Never skip post-processing steps. All seven sub-steps (4a through 4g) must complete before the first audit.
- Never hand off until the audit score is 100%.
- If the user only provides a URL (no build needed), skip Steps 1-2 and start from Step 3.
- If the Figma file already has the design landed and the user wants to re-run post-processing only, skip Steps 1-3 and run Steps 4a-4g then the audit loop.
- Ask follow-up questions upfront. Do not interrupt mid-workflow to ask clarifying questions.
