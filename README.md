# wais-claude-context

Persistent context repo for Claude sessions working on the WAIS design system (DiskarTech / RCBC fintech).

## Why this repo exists

Working with Claude across multiple sessions requires re-establishing context each time: which design system, which Figma file IDs, which conventions, which working preferences. Manual copy-paste is fragile and grows stale.

This repo solves that. A single bootstrap URL gives Claude everything it needs.

## How it works

1. In a new Claude chat, paste one of the bootstrap prompts (see [Bootstrap prompts](#bootstrap-prompts) below)
2. Claude fetches `CONTEXT.md` from this repo
3. `CONTEXT.md` instructs Claude to load relevant skill files and scenario-specific kickoff prompts
4. Claude proceeds with full context, no copy-paste needed

When WAIS changes (new tokens, component updates, etc.), update the relevant skill files here. Next Claude chat automatically picks up the changes.

## Repo structure

```
wais-claude-context/
├── CONTEXT.md                    ← Claude reads this first
├── README.md                     ← this file
├── skills/
│   ├── wais-tokens.md            ← variables, text styles, effect styles
│   ├── wais-components.md        ← component keys, variants, conventions
│   ├── wais-figma-gotchas.md     ← Plugin API traps and workarounds
│   └── wais-screens.md           ← screen-building patterns
├── manifest/
│   └── wais-v0.7.json            ← baseline state for drift detection
├── tools/
│   ├── wais-inventory-snippet.js ← Figma state capture
│   └── wais-doc-drift-checker.py ← validation script
└── kickoff-prompts/              ← scenario-specific prompts
    ├── default.md
    ├── storybook-phase-d.md
    ├── new-screen-build.md
    ├── audit-and-fix.md
    └── token-update.md
```

## Bootstrap prompts

Paste one of these at the start of a new Claude chat. Replace `USER` with your GitHub username.

### General (Claude picks scenario based on conversation)

```
Load my project context from:
https://raw.githubusercontent.com/USER/wais-claude-context/main/CONTEXT.md

Then read kickoff-prompts/default.md from the same repo.
I'll tell you what we're working on next.
```

### Specific scenario

```
Load my project context from:
https://raw.githubusercontent.com/USER/wais-claude-context/main/CONTEXT.md

Scenario: storybook-phase-d
```

Available scenarios: `storybook-phase-d`, `new-screen-build`, `audit-and-fix`, `token-update`, `default`

### Private repo variant

If this repo is private, generate a GitHub Personal Access Token (PAT) with `repo` scope and use authenticated raw URL:

```
Load my project context from:
https://raw.githubusercontent.com/USER/wais-claude-context/main/CONTEXT.md?token=YOUR_PAT

Scenario: storybook-phase-d
```

Note: PAT-in-URL is convenient but the token will appear in chat history. For higher security, use Claude Projects feature with manual file uploads instead.

## Updating the context

When WAIS changes happen:

1. Update the relevant `skills/*.md` file(s)
2. If tokens/styles/components changed, regenerate `manifest/wais-v0.7.json`:
   - Run `tools/wais-inventory-snippet.js` in a Figma `use_figma` call
   - Save output as `manifest/wais-vX.X.json` (new version) or replace existing
3. Update version number in `CONTEXT.md` if a new WAIS publish
4. `git commit && git push`
5. Next Claude session sees the updates automatically

## Updating scenarios

Add new files to `kickoff-prompts/` for new use cases. Update `CONTEXT.md` table of scenarios.

## Drift detection

After each WAIS publish:

```bash
# 1. Run the inventory snippet in Figma against WAIS source
#    Save output as wais-actual.json

# 2. Compare against baseline manifest
python tools/wais-doc-drift-checker.py manifest/wais-v0.7.json wais-actual.json

# 3. Script reports missing/extra/drift, exits 0 (clean) or 1 (drift)
```

## License

Internal RCBC fintech use. Not for public distribution unless specifically authorized.

## Maintainer

Luigi-John Canosa — Product Design Lead, RCBC fintech (DiskarTech)
