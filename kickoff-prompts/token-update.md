# Token update kickoff

User wants to add or modify WAIS source: tokens (variables), text styles, effect styles, or components.

## Required skill loads

- `skills/wais-tokens.md`
- `skills/wais-figma-gotchas.md`
- `skills/wais-components.md` (if components affected)

## Workflow

1. **Confirm scope** — what's being added/changed:
   - New variable in which collection (Primitives / Tokens / Semantic)?
   - New text style with what spec?
   - New component variant / size / appearance?
   - Modification to existing?

2. **Discover constraints** — before adding:
   - Check if it already exists (search WAIS source)
   - Check naming conventions (e.g., `Surface/Brand-*` for branded surfaces)
   - Check alias chain (Semantic → Tokens → Primitives)

3. **Plan in `yaml` block**:
   ```yaml
   change_plan:
     type: variable | text-style | effect-style | component
     name: Surface/...
     collection: 3. Semantic (or which)
     value: alias to Color/.../X | literal | etc.
     description: clear intent statement (100% coverage in Semantic)
     documentation_updates_needed:
       - skills/wais-tokens.md row
       - manifest/wais-vX.X.json entry
       - WAIS Typography page (if text style)
       - WAIS Color page (if color variable)
       - WAIS Changelog page (entry under current version)
     consumer_migration_needed:
       - Which existing inline bindings should migrate to this new token
       - Which screens are affected
     republish_required: yes (Plugin API can't publish)
   ```

4. **Execute the WAIS source change** via `use_figma`
   - For variables: `figma.variables.createVariable(name, collection, type)` + `setValueForMode`
   - For text styles: `figma.createTextStyle()` + set font/size/lineHeight/letterSpacing
   - For components: clone existing variant, rename, modify, `componentSet.appendChild()`

5. **Update WAIS documentation pages**:
   - Typography page: add new row in Type scale section
   - Color page: add new swatch
   - Changelog page: add entry under current version (use `insertChild(1, ...)` due to auto-layout — see gotcha #18)

6. **REMIND USER TO REPUBLISH** — Plugin API can't publish. Wait for confirmation before proceeding to consumer migration.

7. **After republish** — execute consumer migration:
   - Import new token/style/component via `importComponentByKeyAsync` / `importStyleByKeyAsync` / `variables.importVariableByKeyAsync`
   - Bind to existing usages that should migrate
   - Render-verify

8. **Update skill docs and manifest**:
   - Edit `skills/wais-tokens.md` (or whichever)
   - Regenerate `manifest/wais-vX.X.json` via inventory snippet
   - Commit + push so future sessions see the update

9. **Final delivery summary** with:
   - What was added/changed in WAIS
   - Documentation pages updated
   - Consumer migrations applied
   - Skill files updated (commit hash)
   - Honest scope notes

## Special note: Skill file commits

After ANY WAIS change, the skill files in this repo MUST be updated. Drift in the skill files defeats the whole bootstrap system.

Steps:
1. Identify which skill files need updates
2. Edit them in this conversation (using create_file / str_replace if tools available, or paste content for user to commit)
3. Generate updated manifest JSON
4. Tell user: "Please commit and push these changes to wais-claude-context repo"
5. Confirm commit before ending session

## Gotchas to remember

- Plugin API can't publish library — ALWAYS remind user to publish manually
- Library cache: consumer instances don't auto-refresh layout after republish — must `swapComponent` to force re-sync
- `swapComponent` resets vector fill bindings — rebind after
- New text styles need correct `lineHeight` unit (PIXELS or PERCENT) and `letterSpacing` unit (PIXELS or PERCENT)
- Variable aliases: `setValueForMode(modeId, { type: "VARIABLE_ALIAS", id: targetVar.id })`

## Communication style

- Skip apologies. Direct.
- Always show the publish reminder in a visible callout.
- Always offer skill file updates as part of the deliverable, not as an afterthought.
