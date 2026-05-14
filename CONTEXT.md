# WAIS Claude Context — Bootstrap

You are Claude, working with **Luigi-John Canosa** (Product Design Lead, RCBC fintech / DiskarTech).

This repo is the **persistent context** for all WAIS (DiskarTech Web Application Interface System) design system work. The user references this repo at the start of every new chat so you have continuity across sessions without copy-paste.

---

## How to use this bootstrap

1. Read this file first
2. Note which scenario the user named in their kickoff message
3. Fetch the scenario-specific prompt from `kickoff-prompts/<scenario>.md`
4. Load the relevant skill files from `skills/` based on the scenario's needs
5. Acknowledge context loaded, then proceed per the scenario prompt

If the user did NOT specify a scenario, fetch `kickoff-prompts/default.md` and follow its guidance.

---

## Project facts (always relevant)

- **WAIS Figma source**: `qzjPZX4jHQTgRoUgkySmWT`
- **Consumer file (DT-Onboarding-Screens)**: `h67YISYlUdfkhUECHpYh8w`
- **WAIS library_key**: `lk-d9678f4d9388b0c8dacade8f46b2a49d7f60a1b9881ee8783fdf1528219dc1465365e5846ab87ee5745c0962036fa0b44d2cebbdfe13a7b80aea8525cbf7fec9`
- **Current WAIS version**: v0.7 (published 2026-05-13)
- **Project Coffee target**: Q3 2026

## User working preferences (always honored)

- **Stack**: Next.js + Tailwind + TypeScript + Storybook
- **Methodology**: Atomic Design, WCAG accessibility, semantic HTML + ARIA by default
- **Communication**: Lead-to-peer. Skip apologies, filler, hedging. Brief and direct.
- **Code style**: Clean, exemplary, JSDoc on complex logic. Show diffs for minor fixes; rewrite full files only when needed for context.
- **Mentorship**: User mentors junior designers. Code should be documentation-worthy.
- **Visual work**: User shoots Fujifilm X-T5, uses Amaran PT lights, smoke effects. Cinematic/atmospheric lens when relevant.
- **Honesty**: User values direct disclosure of constraints, gotchas, and limitations. State scope honestly. No oversell.

## Skill files (load based on scenario)

| Skill | Path | When to load |
|---|---|---|
| wais-tokens | `skills/wais-tokens.md` | Any work involving colors, text styles, variables, or design tokens |
| wais-components | `skills/wais-components.md` | Any work using WAIS components (Buttons, Icon Buttons, Status Bar, etc.) |
| wais-figma-gotchas | `skills/wais-figma-gotchas.md` | ALWAYS when using `use_figma` tool — non-negotiable |
| wais-screens | `skills/wais-screens.md` | Building or modifying consumer screens |

## Tools

| Tool | Path | Use |
|---|---|---|
| Inventory snippet | `tools/wais-inventory-snippet.js` | Run in Figma to dump current WAIS state |
| Drift checker | `tools/wais-doc-drift-checker.py` | Compare manifest vs actual to detect drift |

## Manifest

`manifest/wais-v0.7.json` is the canonical baseline state. After each WAIS publish, regenerate via inventory snippet and update this manifest.

## Kickoff prompts (scenario-specific)

| Scenario | Path | Description |
|---|---|---|
| `storybook-phase-d` | `kickoff-prompts/storybook-phase-d.md` | Standing up Storybook + repo + Code Connect |
| `new-screen-build` | `kickoff-prompts/new-screen-build.md` | Build new consumer screens in Figma |
| `audit-and-fix` | `kickoff-prompts/audit-and-fix.md` | Sweep, audit, remediate consumer file drift |
| `token-update` | `kickoff-prompts/token-update.md` | Add/modify WAIS tokens, styles, or components |
| `default` | `kickoff-prompts/default.md` | Catch-all when no scenario specified |

---

## Critical rules (always)

1. **Plugin API cannot publish a library** — user must click "Publish Library" in Figma UI
2. **`use_figma` requires font loading** — load all 9 Work Sans + Inter weights before any text op
3. **Render-verify after every visual change** — call `figma_get_component_image` to confirm
4. **Always offer to update skills/manifest** when WAIS changes happen — drift kills the system
5. **Honest scope notes always** — if a workaround is needed, say so explicitly

## Communication format

User prefers structured responses with `yaml` code blocks for planning and decision points. Inline prose for execution and explanation. NO tappable buttons / `ask_user_input_v0` — straight conversation only.

---

After reading this file, respond with:

1. ✅ Context loaded confirmation (one line)
2. Which scenario you detected (or asked which to use if unclear)
3. Then proceed per the scenario prompt
