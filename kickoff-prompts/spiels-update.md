# Scenario: spiels-update

Trigger: user wants to add, modify, or roll out localized strings (spiels) in WAIS or a consumer file. Use this when the request involves multi-language support, language mode switching, or string variables.

## Required skills to load

1. `skills/wais-spiels.md` — non-negotiable
2. `skills/wais-figma-gotchas.md` — non-negotiable for any `use_figma` work
3. `skills/wais-tokens.md` — load if the work touches text styles or color bindings alongside strings
4. `skills/wais-components.md` — load if the work involves binding inside component instances (Button labels, Text Field labels)

## Standard workflow

```yaml
spiels_update:
  step_1_inspect:
    - Open the target screen / component / file
    - List every user-facing string
    - For each string, classify:
        screen_scoped: <screen>/<element>
        shared:        shared/<context>/<element>
        component:     component/<comp>/<element>
        code_only:     code-only/<screen>/<element>  (interpolated/pluralized)
    - Flag any strings inside component instances (need source-component path)

  step_2_collection:
    - Check if "WAIS / Strings" collection exists
    - Create if missing, with EN as default mode
    - Add additional language modes per user direction (TL, CB, etc.)

  step_3_create:
    - Batch-create string variables
    - HARD RULE: scope = ["TEXT_CONTENT"] only
    - Set values for every mode (EN, TL, CB, ...)

  step_4_bind:
    - For each text node: load all fonts via getRangeFontName loop
    - setBoundVariable("characters", variable)
    - Skip nodes inside instances — flag for source-component update
    - Return mutated node IDs

  step_5_verify:
    - Render screenshot at default mode (EN)
    - Switch frame mode to TL → screenshot → confirm
    - Switch frame mode to CB → screenshot → confirm
    - Report any layout issues from string length differences

  step_6_handoff:
    - Generate matching messages/*.json for next-intl / i18next
    - Note which strings are code-only (interpolation, pluralization)
    - Remind user to Publish Library
    - Offer to update skills/wais-spiels.md if new patterns emerged
```

## Decision points (always confirm before proceeding)

1. **Which languages?** — confirm exact mode set before creating collection
2. **Naming convention?** — default screen-scoped; confirm if user wants flat semantic or component-scoped
3. **Scope of rollout** — single screen, full flow, or system-wide?
4. **Component instance strings** — modify source component, or detach? (default: source component)

## Honest scope notes (always disclose)

- Figma string variables do **not** support pluralization (ICU MessageFormat) or runtime interpolation — those stay in code
- Strings inside component instances cannot be bound directly — requires source-component update or property binding
- Publish Library is a manual step — plugin API cannot trigger it
- Switching mode at the page level swaps everything; frame-level overrides are for side-by-side comparison only

## Output expectations

- Always show: collection ID, mode IDs, created variable IDs, mutated node IDs
- Always show: a verification screenshot per language mode if more than 1-2 strings were bound
- Always offer: matching `messages/<lang>.json` snippet for engineering handoff
- Always conclude with: open issues (instance-bound strings not yet handled, shared strings to promote, etc.)
