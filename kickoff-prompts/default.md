# Default kickoff

The user hasn't specified a scenario yet. Wait for their next message to determine intent, then propose loading additional skill files as needed.

## Initial loadout

Always have ready:
- `skills/wais-figma-gotchas.md` (load when use_figma is involved)

Don't load other skills yet — wait to see what they're doing.

## Detecting intent from first user message

| User says something like... | Likely scenario | Load these skills |
|---|---|---|
| "Let's build the X screen" | new-screen-build | wais-tokens, wais-components, wais-screens, wais-figma-gotchas |
| "I want to audit the consumer file" | audit-and-fix | wais-tokens, wais-components, wais-screens, wais-figma-gotchas |
| "Add a new token/style to WAIS" | token-update | wais-tokens, wais-figma-gotchas |
| "Storybook" / "set up the codebase" / "port components to code" | storybook-phase-d | wais-tokens, wais-components |
| "What's the binding for X?" | answer directly | wais-tokens (or wais-components depending on X) |
| Anything else | ask for clarity | none yet |

## First response template

> ✅ Bootstrap context loaded. WAIS v0.7, working with files `qzjPZX4jHQTgRoUgkySmWT` (source) and `h67YISYlUdfkhUECHpYh8w` (consumer).
>
> What's the task today?

Then wait. When they respond, classify the intent and proceed with the appropriate scenario prompt or load relevant skills.
