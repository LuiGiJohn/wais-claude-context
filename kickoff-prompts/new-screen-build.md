# New screen build kickoff

User wants to build new consumer screen(s) in the DT-Onboarding-Screens file using WAIS components and tokens.

## Required skill loads

Load all of these at session start:
- `skills/wais-tokens.md` — token reference
- `skills/wais-components.md` — component keys
- `skills/wais-screens.md` — screen-building patterns and conventions
- `skills/wais-figma-gotchas.md` — Plugin API traps (MANDATORY before any use_figma call)

## Standard build workflow

1. **Ask user** for:
   - Reference design (Figma node ID in the reference file `ocbGSr4s7lU6UBgyYlhWea`, or a description)
   - Target placement (which page + section + section-relative position)
   - Screen name convention (e.g., "Profile / Default")

2. **Inspect reference** via `get_design_context` if Figma node provided

3. **Plan in `yaml` block** before building:
   - Layout structure (content frame, chrome, sub-frames)
   - Component instances needed (with keys from wais-components.md)
   - Token bindings expected (with keys from wais-tokens.md)
   - Honest scope notes (any gaps in WAIS that need workarounds)

4. **Build incrementally** with render-verify between phases:
   - Phase 1: Screen frame + content shell
   - Phase 2: Main content (cards, forms, etc.)
   - Phase 3: Chrome (Status Bar, Controls, Home Indicator)
   - Phase 4: Scroll pattern (numberOfFixedChildren, overflowDirection)

5. **After each phase**: `figma_get_component_image` to verify

6. **Final audit**: walk the new screen, count bound fills/strokes/text styles, report
   - 0 hardcoded fills (excluding white/black)
   - 0 hardcoded strokes
   - 100% text nodes have textStyleId (or noted exception)

## Required conventions (per wais-screens.md)

- Mobile frame: 402×874
- `Surface/Page` bg
- Status Bar: Light for dark/image bgs, Dark for white bgs
- Back/close button: **Always** Icon Button Tertiary/Filled/Default — never custom
- List items: 354×56, Body/Default labels (NOT Body/Large)
- Chrome at END of children, `numberOfFixedChildren` counts from end
- `overflowDirection = "VERTICAL"` if scrollable, `"NONE"` otherwise

## Critical gotchas to remember

- Font loading mandatory (all 9 Work Sans + Inter weights)
- `swapComponent` resets vector fill bindings — always re-bind after swap
- `SECTION.appendChild` reinterprets coords as section-relative
- `layoutSizingHorizontal` requires parent IS auto-layout + node already appended

## Honest scope reminders

- Always offer to update consumer file audit after building
- If a WAIS gap is hit (missing token, missing style, missing icon), flag honestly:
  - Use closest substitute
  - Document the gap in scope notes
  - Suggest WAIS update for next publish cycle

## Communication style

- Skip apologies. Direct.
- `yaml` planning blocks before each phase.
- Render-verify visually after every phase — no "looks fine" without screenshot.
- End with structured delivery summary listing what's done + scope notes.
