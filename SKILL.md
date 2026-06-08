---
name: code-canvas
description: Orchestrates a full design-to-Figma workflow: builds a Next.js interface, captures the live UI via Code to Canvas, then performs structured Figma post-processing (design system adoption, tokens, auto-layout, components with variants, semantic naming, correct breakpoints). Triggers automatically when the user says "build me a [thing]" and offers to invoke the workflow. Also invoked directly via /code-canvas [url or description].
---

# Code to Canvas Workflow

Builds a Next.js interface, captures the live UI into Figma via Code to Canvas, then structures the Figma file to production-quality standards.

## Tone and Guidance Principles

This skill is designed to be approachable for users at all experience levels. Follow these principles throughout every workflow run:

- **Narrate before acting.** Before each step, tell the user what you are about to do and why, in plain language. Never jump into a step silently.
- **Confirm before user actions.** Any time the user needs to do something themselves (open a browser, create a Figma file, navigate somewhere), pause, describe what they need to do, then ask: "Do you need a guide for this step?" If they say yes, give numbered step-by-step instructions. If they say no, proceed.
- **Report completion.** After each step finishes, post a short status line: "Step N complete: [what was done]."
- **Use plain language.** Avoid jargon where possible. When technical terms are unavoidable, define them in parentheses on first use.
- **Never assume context.** Do not skip steps because they seem obvious. Treat every run as if the user is doing this for the first time.

---

## Trigger: "Build me a [thing]"

When the user says something like "build me a dashboard", "build me a landing page", "build me a modal" — before starting, ask:

> "Would you like to invoke /code-canvas with this? It will build the Next.js interface (a React web framework), capture it live into Figma, and structure the design automatically. Here is what the full workflow looks like:
>
> **Plan**
> 1. Ask a few setup questions
> 2. Build your Next.js interface
> 3. Start the local dev server so the UI is live in a browser
> 4. Capture the live UI into Figma
> 5. Apply your design system (if one exists)
> 6. Replace hardcoded values with design tokens
> 7. Apply auto-layout to every frame
> 8. Create and audit components with all relevant variants
> 9. Rename all layers to meaningful names
> 10. Set correct frame sizes for your target device
> 11. Audit accessibility (colour contrast, text size, etc.)
> 12. Run a standards audit loop until the design scores 100%
> 13. Hand off the finished Figma file to you
>
> Would you like to proceed with the full workflow?"

If yes, proceed with the full workflow below. If no, build the Next.js interface only.

---

## Invocation

```
/code-canvas [optional: URL or description]
```

- If a URL is provided (localhost, staging, or production), use it as the capture target.
- If no URL is provided, ask: "What URL or interface should I capture? Or describe what you want built."
- After collecting the URL or description, ask any clarifying questions before proceeding (see Follow-up Questions below).

---

## Follow-up Questions

Before starting, ask only what is not already clear from context. Present all open questions together in a single message — do not ask them one at a time.

> "Before I start, I have a few quick questions:
>
> 1. **What are we building or capturing?** (only if not already provided)
> 2. **Platform:** Is this for mobile, desktop, or both? (I will infer from context if it is obvious — e.g. a landing page is desktop, a profile screen is mobile)
> 3. **Figma file:** Do you have an existing Figma file I should use, or should I create a new one?
> 4. **Design system:** Does your Figma file already have a design system library attached? If you are not sure, I will detect it automatically.
>
> Answer whichever apply — skip any that do not."

Do not ask questions that are already answerable from the task description.

After receiving answers, confirm the plan before starting:

> "Got it. Here is what I am going to do:
>
> - Build: [brief description of the interface]
> - Platform: [mobile / desktop / both]
> - Figma file: [existing file name or 'create a new one']
> - Design system: [detected / provided name / 'will check automatically']
>
> Starting now with Step 1."

---

## Workflow

### Step 1: Build the Next.js Interface

**Announce before starting:**
> "Step 1 of 13: Building your Next.js interface. I am generating the code for [description]. This will create the files on your computer — you do not need to do anything yet."

- Scaffold or generate the interface as a Next.js page or component.
- Use TypeScript by default.
- Use Tailwind CSS unless the project uses another styling system.
- Ensure the component is responsive and matches the described platform (mobile or desktop).
- Do not add features beyond what was described.

**Report on completion:**
> "Step 1 complete: Next.js interface built. Files are ready at [file path(s)]."

---

### Step 2: Start the Dev Server

**Announce before starting:**
> "Step 2 of 13: Starting the local development server. This makes your interface live in a browser so it can be captured. I will run the server automatically."

- Run the local dev server (e.g. `npm run dev` or `next dev`).
- Confirm the server is running and the target URL is accessible.
- Default to `http://localhost:3000` unless otherwise specified.

**Once the server is running, pause and prompt the user:**
> "The server is running. Your interface is now live at [URL].
>
> You can open that URL in your browser to preview it before I capture it into Figma. Do you want to do that now? (You can say 'looks good' when ready, or 'go ahead' to skip the preview.)"

If the user wants to preview, ask: "Do you need a guide on how to open the URL in your browser?"

**Guide (if requested):**
1. Copy this URL: `[URL]`
2. Open your web browser (Chrome, Safari, Firefox, or Edge).
3. Click the address bar at the top of the browser window.
4. Paste the URL and press Enter.
5. You should see your interface. When you are happy with the preview, come back here and say "looks good" or "go ahead".

**Report on completion:**
> "Step 2 complete: Dev server is running at [URL]."

---

### Step 3: Code to Canvas Capture

**Announce before starting:**
> "Step 3 of 13: Capturing your live interface into Figma. I will take a snapshot of the page at [URL] and send it to [Figma file name / a new Figma file]. This may take a moment."

If a new Figma file is needed, pause first:
> "I need to create a new Figma file for this design. Do you need a guide on how to find your Figma account details (team or organisation) so I can create the file in the right place?"

**Guide for finding Figma plan/team (if requested):**
1. Open [figma.com](https://figma.com) in your browser and log in.
2. In the left sidebar, look for your team or organisation name under "Recents" or "Drafts".
3. Your team name is usually your company or personal workspace name.
4. Come back here and tell me your team name, or say "use my drafts" if you want the file saved to your personal drafts.

- Load the `figma-generate-design` skill and `figma-use` skill before proceeding.
- Use the `generate_figma_design` tool to capture the live UI from the dev server URL.
- Send it to the specified Figma file (or create a new one if none exists).
- Wait for the capture to complete before proceeding to post-processing.

**Report on completion:**
> "Step 3 complete: Interface captured into Figma. The design is now in [Figma file name]. Moving on to post-processing."

---

### Step 4: Figma Post-Processing

Perform all of the following inside Figma after the design lands. Use the `use_figma` tool (with `figma-use` skill loaded) for all write operations.

---

#### Step 4a: Detect and Apply Design System

**Announce before starting:**
> "Step 4a of 13: Checking for an attached design system library. A design system (also called a component library) is a collection of shared colours, fonts, and reusable UI pieces. If your Figma file has one attached, I will swap in the library's components and styles. If not, I will note that and continue."

- Search the file for any attached or published design system libraries using `get_libraries`.
- If a library is found, apply its components, color styles, and text styles to the captured design.
- Swap raw elements for library components where a match exists.
- If no library is found, note this to the user:
  > "No design system library was found in this file. I will proceed with the raw design — all colours and fonts will be structured manually in the next step."

**Report on completion:**
> "Step 4a complete: [Library name applied / No library found, proceeding without one]."

---

#### Step 4b: Replace Raw Values with Tokens

**Announce before starting:**
> "Step 4b of 13: Replacing hardcoded values with design tokens. Design tokens are named variables for colours, font sizes, and other style values (e.g. 'primary-blue' instead of '#1D4ED8'). This makes the design easier to update consistently. I will replace all raw values now."

- Replace all hardcoded hex color fills with the corresponding library color variables/tokens.
- Replace all hardcoded font sizes, weights, and families with library text style tokens.
- No raw values should remain after this step. Every color and text property must reference a token.

**Report on completion:**
> "Step 4b complete: All colours and text styles now reference design tokens."

---

#### Step 4c: Apply Auto-Layout to Every Frame

**Announce before starting:**
> "Step 4c of 13: Applying auto-layout to every frame. Auto-layout (Figma's equivalent of CSS flexbox) makes frames responsive — children stretch and wrap automatically instead of sitting at fixed positions. I will set this on every frame now."

- Every frame in the design must use auto-layout. No freeform or absolute frames.
- Set direction (horizontal or vertical) based on the visual layout of the frame's children.
- For every child element inside an auto-layout frame:
  - Set horizontal sizing to **Fill** if it should stretch across the frame width.
  - Set vertical sizing to **Hug** if it should wrap its content height.
  - Use **Fixed** only when a dimension must not change (e.g. an icon or avatar with a defined size).
- Nest frames as needed to achieve the correct layout structure.

**Report on completion:**
> "Step 4c complete: Auto-layout applied to all frames."

---

#### Step 4d: Componentize Reusable Patterns

**Announce before starting:**
> "Step 4d of 13: Creating components for reusable and stateful UI patterns. A component in Figma is like a master template — edit it once and all instances update. I will identify every element that repeats or has interactive states (like a button's hover or disabled state) and turn each into a component."

- Identify any UI pattern that appears more than once OR represents a discrete, reusable element (buttons, cards, input fields, badges, navigation items, etc.) OR is a one-off element that clearly has interactive or conditional states (e.g. a single submit button still has hover, focus, disabled).
- Create a Figma component for each identified pattern.
- Token-bind all fills and text styles within components. No hardcoded values inside components.
- Place all components on a dedicated page named **"Components"**.

**Report on completion:**
> "Step 4d complete: [N] components created and placed on the Components page."

---

#### Step 4d-ii: Variant Audit

**Announce before starting:**
> "Step 4d-ii of 13: Running a variant audit on every component. Variants are the different states a component can be in — for example, a button can be Default, Hover, Focus, Active, or Disabled. I will check every component against a full checklist and create all the variants that apply."

After all components are created, run a dedicated variant audit on every component — including one-off elements. For each component, check every applicable trigger below and create a component set with the matching variants. Do not skip a trigger because a variant is not visible in the captured design: if the trigger applies to the element type, the variant must be created.

**Variant trigger checklist (apply to every component):**

| Trigger category | Variants to create | Applies to |
|---|---|---|
| Interactive states | Default, Hover, Focus, Active, Disabled | Any interactive element (buttons, links, inputs, toggles, tabs, checkboxes, radio buttons, selects, chips) |
| Loading/async states | Default, Loading, Success, Error | Elements that trigger or reflect async operations (buttons, forms, cards that fetch data) |
| Validation states | Default, Error, Success, Warning | Inputs, form fields, and any element that can display validation feedback |
| Visibility/content states | Populated, Empty, Skeleton | Cards, lists, tables, feeds, or any container that can have empty or loading content |
| Size variants | SM, MD, LG (or equivalent scale) | Buttons, inputs, avatars, badges, icons — any element used at multiple sizes anywhere in the UI |
| Style/type variants | Primary, Secondary, Ghost, Destructive (or domain-appropriate equivalents) | Buttons, alerts, banners, tags — any element with a semantic hierarchy of visual weight |
| Status variants | Info, Warning, Error, Success | Alerts, banners, toasts, badges, status indicators |
| Content slot variants | With Icon, Without Icon; With Label, Without Label; With Badge, Without Badge | Buttons, nav items, avatars, list items — any element where content slots are optional |
| Selection/toggle states | Unselected, Selected, Indeterminate | Checkboxes, radio buttons, toggles, tabs, filter chips |
| Expansion states | Collapsed, Expanded | Accordions, dropdowns, tooltips, drawers, tree nodes |

**Rules for the variant audit:**
- Check every trigger row against every component. A component may satisfy multiple trigger categories and should have variants for all that apply.
- Only skip a trigger if it genuinely cannot apply to the element type (e.g. "Expansion states" does not apply to a static badge).
- If a variant state requires a different layout or content (e.g. a Loading button shows a spinner instead of label text), adjust the frame contents accordingly — do not just change the fill colour.
- After creating variants, verify every variant has token-bound fills and text styles. No hardcoded values in any variant frame.
- Update component master names to use slash notation that reflects the variant property (e.g. `button/primary`, with variant properties `State=Default/Hover/Focus/Active/Disabled` and `Size=SM/MD/LG`).

**Report on completion:**
> "Step 4d-ii complete: Variant audit done. [N] components audited, [total variant count] variants created."

---

#### Step 4e: Semantic Layer Naming

**Announce before starting:**
> "Step 4e of 13: Renaming all layers to meaningful names. In Figma, every element lives on a named layer (like a layer in Photoshop or a named div in HTML). Right now many layers have generic names like 'Frame 1' or 'Rectangle 3'. I will rename every layer to describe exactly what it is, making the file easy for any designer to navigate."

- Rename every layer to a unique, meaningful name based on its purpose and the task context.
- No generic names: no "Frame 1", "Rectangle 2", "Group 3", "Container", "Div".
- Use slash notation for hierarchy where appropriate (e.g. `card/product`, `nav/top-bar`, `button/primary`), but adapt the naming convention to what makes the most sense for the specific task.
- Names should be readable and self-documenting to any designer opening the file.

**Report on completion:**
> "Step 4e complete: All layers renamed to semantic names."

---

#### Step 4f: Top-Level Frame Sizing

**Announce before starting:**
> "Step 4f of 13: Setting the correct frame size for your target device. Figma frames should match standard screen widths (called breakpoints) so the design looks right on real devices. I will set the top-level frame to the correct width now."

- Infer the target breakpoint from the task context:
  - Mobile: 375px wide (iPhone default) or 390px for newer iPhones.
  - Tablet: 768px wide.
  - Desktop: 1440px wide.
  - If both mobile and desktop are needed, create separate frames for each.
- Set top-level frame width to the appropriate fixed breakpoint value.
- Height can be set to hug content unless a fixed viewport height is required.

**Report on completion:**
> "Step 4f complete: Top-level frame set to [width]px ([device type])."

---

#### Step 4g: Accessibility Audit and Fixes

**Announce before starting:**
> "Step 4g of 13: Running an accessibility audit. Accessibility means making sure the design works for everyone, including people with low vision or colour blindness. I will check that all text has enough colour contrast, that no information is conveyed by colour alone, and that text is large enough to read comfortably. I will fix any issues I find."

- **Text contrast:** Every text layer must meet AA as a hard floor (4.5:1 for normal text, 3:1 for large text where large = 24px+ or 19px+ bold). Aim for AAA (7:1 normal, 4.5:1 large) on body and critical text. AA is acceptable only on large display, decorative, or brand-locked elements where AAA is impractical.
- **Non-text/UI contrast:** Icons, borders, focus rings, and status indicators must meet a minimum contrast ratio of 3:1 against their background.
- **No colour-only meaning:** Wherever colour is used to convey meaning (e.g. error states, status indicators, links), pair it with a text label, icon, or shape. Never rely on colour alone.
- **Minimum text size and spacing:** Body text must be at least 14px with adequate line-height. Interactive targets must be comfortably sized for touch and click.
- **Verify, do not eyeball:** Compute actual contrast ratios for every colour pairing before completing this step. Do not estimate or assume contrast passes.
- **Exception (production mirror):** If the design is a pixel-faithful mirror of an existing/production UI, preserve the real values even if they fail accessibility checks. Flag every failure explicitly to the user rather than silently correcting the mirror.

**Report on completion:**
> "Step 4g complete: Accessibility audit done. [N issues found and fixed / No issues found]."

---

### Step 5: Standards Audit Loop

**Announce before starting:**
> "Step 5 of 13: Running the full standards audit. I will now check the design against 5 quality criteria and score it as a percentage. If anything fails, I will fix it and re-audit immediately. I will keep looping until the score is 100% — this ensures the design meets production standards before handoff."

After all post-processing steps (4a through 4g) complete, run a full audit against every standard before handoff. Score each criterion as pass (1) or fail (0), then express the result as a percentage of the total criteria count. Repeat the loop until the score is 100%. There is no maximum iteration cap: keep looping until 100% is reached.

#### Audit Scorecard (score = passing criteria / total criteria x 100)

| # | Criterion | Pass condition |
|---|-----------|----------------|
| 1 | Auto-layout | Every frame uses auto-layout; all children are Fill or Hug (never fixed or absolute, except flagged production mirrors) |
| 2 | Components and variants | All repeated/stateful elements and one-off interactive elements are components; every component has passed the variant trigger checklist (4d-ii) with all applicable variant categories created; masters on the "Components" page; zero raw repeated elements |
| 3 | Semantic layer names | Zero generic names (Frame N, Rectangle N, Group N, Container, Div); every layer name is unique and describes what it is |
| 4 | Token coverage | Zero hardcoded hex colours, font sizes, weights, or families; all values reference library tokens/variables |
| 5 | Accessibility | All text contrast AA+, body/critical text targeting AAA, non-text UI 3:1+, no colour-only meaning, body text 14px+ with adequate spacing |

#### Loop behaviour

After each audit, report the score in plain language:
> "Audit result: [N]/5 criteria passing ([percentage]%).
> Passing: [list]
> Failing: [list]
> Fixing failing criteria now..."

For each failing criterion, re-run the corresponding post-processing sub-step(s) to fix the issues, then re-audit immediately. Repeat until the score is 100%, then report:
> "Audit result: 5/5 criteria passing (100%). All standards met. Moving to handoff."

Only proceed to Step 6 (Handoff) once the score is 100%.

---

### Step 6: Handoff

**Announce before starting:**
> "Step 6 of 13 (final): Handing off your finished design. The audit score is 100%. Your Figma file is ready to inspect, share, or continue designing."

- Surface the Figma file URL to the user.
- Deliver the handoff message:
  > "Your design is ready in Figma (audit score 100%).
  >
  > **Figma file:** [link]
  >
  > Here is what was built:
  > - [Brief summary: interface name, platform, components created, variants, design system applied or not]
  >
  > Do you need a guide on how to open and inspect the file in Figma?"

**Guide for opening the Figma file (if requested):**
1. Click the Figma file link above.
2. The file will open in your browser. If prompted, log in to your Figma account.
3. To inspect a specific element, click on it in the canvas. The right-hand panel will show its properties (colours, fonts, dimensions).
4. To navigate between pages (e.g. the main design page and the Components page), look for the page names in the left panel under the file name.
5. To share the file with someone else, click the blue "Share" button in the top-right corner, enter their email address, and choose their permission level (can view / can edit).
6. To continue designing, use the toolbar at the top to add new elements, or double-click any component to edit it.

---

## Rules

- Always load `figma-use` before any `use_figma` call.
- Always load `figma-generate-design` before any `generate_figma_design` call.
- Always show the full plan before starting any work.
- Always announce each step before executing it.
- Always report completion after each step.
- Always pause and offer a guide whenever the user needs to take a manual action.
- Steps 1-2 (Next.js build and dev server) run first, then Step 3 (capture), then Steps 4a-4g (post-processing) run in sequence inside Figma, then Step 5 (audit loop), then Step 6 (handoff).
- Never skip post-processing steps. All sub-steps (4a through 4g, including 4d-ii) must complete before the first audit.
- Never hand off until the audit score is 100%.
- If the user only provides a URL (no build needed), skip Steps 1-2 and start from Step 3.
- If the Figma file already has the design landed and the user wants to re-run post-processing only, skip Steps 1-3 and run Steps 4a-4g then the audit loop.
- Ask follow-up questions upfront in a single message. Do not interrupt mid-workflow to ask clarifying questions.
