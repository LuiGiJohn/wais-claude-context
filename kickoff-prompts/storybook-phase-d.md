# Storybook Phase D kickoff

User is starting **Phase D**: porting WAIS from Figma → Next.js + Tailwind + Storybook code with Code Connect mappings back to Figma.

## Required skill loads

Load all of these at session start:
- `skills/wais-tokens.md` — all 285 variables, 19 text styles, keys + specs
- `skills/wais-components.md` — 127 components catalogued
- `skills/wais-figma-gotchas.md` — load only if Figma writes happen (likely yes, for Code Connect)

## Context summary

User is Product Design Lead. They have:
- Mature WAIS v0.7 published in Figma
- 22-of-37 fully tokenized consumer screens (DT-Onboarding-Screens)
- Skills bundle v2.0 capturing all conventions

Now they want code parity — design system as React components with Storybook documentation.

## Phase D goals

1. **Token export pipeline** — Figma variables → CSS variables → Tailwind config
2. **Repo architecture** — Next.js + Storybook structure, Atomic Design
3. **First batch of components** — Button, IconButton, TextField, Status Bar
4. **Code Connect mappings** — link each story back to its Figma node
5. **Storybook conventions** — story-per-variant, a11y addon, viewport addon, design tokens addon

## What to propose FIRST (before any code)

Present a `yaml` block with these decisions for user to confirm:

```yaml
phase_d_plan:
  
  repo_setup:
    structure: monorepo (apps/storybook + packages/wais-ui + packages/tokens) OR single repo?
    package_manager: pnpm vs npm vs yarn
    bundler: tsup vs vite vs webpack
  
  token_pipeline:
    approach: 
      - Style Dictionary (declarative, mature)
      - Manual TS export from Figma REST API
      - Tokens Studio for Figma + import to code
      - Custom Plugin API script writing to file
    deliverable: CSS variables + Tailwind config + TS types
  
  first_component:
    Recommend: Button (144 variants exercises whole pipeline + most-used)
  
  storybook:
    version: 8.x latest
    addons: 
      - @storybook/addon-essentials
      - @storybook/addon-a11y
      - @storybook/addon-viewport
      - @storybook/addon-design-tokens (or storybook-figma-export)
      - @figma/code-connect
  
  code_connect:
    strategy: parser-based (auto-detect props) vs template-based (full control)
    
  testing:
    a11y: axe-core via @storybook/test-runner
    visual: Chromatic (optional, paid)
    unit: vitest + @testing-library/react
  
  ci_cd:
    storybook_deploy: Chromatic / Vercel / GitHub Pages?
    publish_pipeline: changesets + semantic versioning?
```

## Required clarifying questions before scaffolding

1. Monorepo or single repo?
2. Hosting target for Storybook (Chromatic, Vercel, GitHub Pages, internal)?
3. Token export tool preference (Style Dictionary is the most boring/safe choice)?
4. Code Connect strategy (parser vs template)?
5. License model (private team workspace, semver release, etc.)?

Only ASK ONE BATCH of questions. After user answers, proceed to scaffold.

## After alignment

Scaffold deliverables (in order):

1. `package.json`, `tsconfig.json`, `.gitignore`
2. `tokens/` — Style Dictionary config + Figma export pipeline
3. `tailwind.config.ts` — generated from tokens
4. `.storybook/` — main.ts, preview.tsx, addons
5. `src/components/atoms/Button/` — first component with stories + Code Connect
6. `figma.config.json` — Code Connect config
7. README.md — usage instructions

## Honest notes to keep visible

- **Code Connect requires CLI auth** — user must run `figma connect` setup locally with their token
- **Style Dictionary doesn't pull Figma directly** — you'll need either Figma REST API token OR a one-time export
- **Plugin API access** — the existing `use_figma` tool can read Figma variables/styles and write them to file; can be paired with Style Dictionary
- **First component takes longest** — Button + Storybook setup is ~2-3 hours of careful work. Subsequent components ~30-60 min each.

## Communication style

- Skip apologies. Direct.
- Show `yaml` planning blocks. Inline prose for execution.
- After scaffolding, recap with structured summary + clear "next step" options.
