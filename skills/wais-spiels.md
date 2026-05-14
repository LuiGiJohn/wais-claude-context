# wais-spiels

**Version**: v0.1 (initial 2026-05-14)
**WAIS source**: `qzjPZX4jHQTgRoUgkySmWT`

Complete reference for **string variable tokens (spiels)** in WAIS — the system for binding user-facing copy to variables so a single mode switch re-renders an entire screen in another language. Use this skill before adding, binding, or modifying any localized string in WAIS or any WAIS consumer file.

This is the i18n companion to `wais-tokens.md`. Where `wais-tokens` covers colors / text styles / shadows, this skill covers **strings**.

## Architectural principles

1. **Strings live in their own collection** — never mix into Semantic, Tokens, or Primitives. Language modes are orthogonal to design tokens; polluting the color collection with language modes forces every consumer to choose a language even if they don't need localization.
2. **Modes = languages, not regions** — `EN` / `TL` / `CB`, not `en-PH` / `tl-PH`. WAIS spiels are language-keyed; regional formatting (dates, currency) is a code-side concern.
3. **One scope only: `TEXT_CONTENT`** — prevents the string from appearing in fill / stroke / radius pickers. Hard rule.
4. **Screen-scoped naming by default** — `login/title`, `cards/empty-state-cta`. Maps cleanly to `useTranslations('login')` namespaces in `next-intl` / `i18next`.
5. **Cross-screen strings get promoted to `shared/`** — once a string appears on 2+ screens, move it to `shared/<context>/<key>` so engineering doesn't ship duplicates.

## Collection: WAIS / Strings

| Property | Value |
|---|---|
| Collection name | `WAIS / Strings` |
| Default mode | `EN` |
| Additional modes | `TL` (Taglish), `CB` (Cebuano) |
| Scope (all variables) | `["TEXT_CONTENT"]` |
| Type | `STRING` only |

Modes are added per language as the system expands. Adding a mode is non-destructive — existing variables auto-inherit the EN value until explicitly overridden.

## Naming conventions

### Screen-scoped (default)

```
<screen>/<element>
```

Examples:
- `login/title`
- `login/subtitle`
- `login/help-link`
- `login/submit-button`
- `cards/limits-reached`
- `transfer/confirm-cta`

### Shared (cross-screen)

```
shared/<context>/<element>
```

Examples:
- `shared/auth/welcome-back`
- `shared/errors/network-failure`
- `shared/cta/try-again`
- `shared/labels/mobile-number`

### Component-scoped (rare — only for true component-level copy)

```
component/<component>/<element>
```

Examples:
- `component/text-field/required-marker` (`*`)
- `component/keyboard/done-key`

Reserve this for strings owned by the component itself, not screen copy that happens to live inside a component instance.

## Initial spiels (login screen — reference set)

These are the v0.1 baseline. Extend per screen as new flows are localized.

| Variable | EN | TL | CB |
|---|---|---|---|
| `login/title` | Log in | Mag-log in | Mosulod |
| `login/subtitle` | Ready to get back to it? Log in to continue your journey. | Ready na ulit sa diskarte? Log in to continue your journey. | Andam na pag-usab? Mosulod aron magpadayon sa imong panaw. |
| `login/welcome-back` | Welcome Back | Welcome Back | Maayong Pagbalik |
| `login/help-link` | Need help logging in? | Kailangan ng tulong mag-log in? | Nanginahanglan og tabang sa pagsulod? |
| `login/submit-button` | Log in | Mag-log in | Mosulod |
| `login/mobile-label` | Mobile Number | Mobile Number | Numero sa Mobile |
| `login/passcode-label` | Passcode | Passcode | Passcode |

Note: TL is intentionally Taglish (English + Tagalog code-switching), matching DiskarTech's brand voice. Pure Tagalog reads stiff to the target user.

## The full binding recipe

### Step 1: Create the collection + modes

```javascript
// Run once per file. Idempotent check: skip if collection exists.
const existing = await figma.variables.getLocalVariableCollectionsAsync();
let collection = existing.find(c => c.name === "WAIS / Strings");

if (!collection) {
  collection = figma.variables.createVariableCollection("WAIS / Strings");
  collection.renameMode(collection.modes[0].modeId, "EN");
  collection.addMode("TL");
  collection.addMode("CB");
}

return {
  collectionId: collection.id,
  modes: collection.modes.map(m => ({ id: m.modeId, name: m.name }))
};
```

### Step 2: Batch-create string variables

```javascript
const collection = (await figma.variables.getLocalVariableCollectionsAsync())
  .find(c => c.name === "WAIS / Strings");
const modes = Object.fromEntries(collection.modes.map(m => [m.name, m.modeId]));

const spiels = [
  { name: "login/title",          EN: "Log in",          TL: "Mag-log in",          CB: "Mosulod" },
  { name: "login/subtitle",       EN: "Ready to get back to it? Log in to continue your journey.",
                                  TL: "Ready na ulit sa diskarte? Log in to continue your journey.",
                                  CB: "Andam na pag-usab? Mosulod aron magpadayon sa imong panaw." },
  // ... etc
];

const created = [];
for (const s of spiels) {
  const v = figma.variables.createVariable(s.name, collection, "STRING");
  v.scopes = ["TEXT_CONTENT"];   // HARD RULE — never ALL_SCOPES
  v.setValueForMode(modes.EN, s.EN);
  v.setValueForMode(modes.TL, s.TL);
  v.setValueForMode(modes.CB, s.CB);
  created.push({ id: v.id, name: v.name });
}

return { created };
```

### Step 3: Bind to text nodes

```javascript
const bindings = [
  { nodeId: "4:1245", varId: "VariableID:6:4" },   // title
  { nodeId: "4:1246", varId: "VariableID:6:5" },   // subtitle
  // ...
];

const loadedFonts = new Set();
const mutatedNodeIds = [];

for (const b of bindings) {
  const node = await figma.getNodeByIdAsync(b.nodeId);
  if (!node || node.type !== "TEXT") continue;

  // Load EVERY font in the text node before binding — see gotcha #1
  const len = node.characters.length;
  const seen = new Set();
  const fontsToLoad = [];
  if (len === 0) {
    fontsToLoad.push(node.fontName);
  } else {
    for (let i = 0; i < len; i++) {
      const f = node.getRangeFontName(i, i + 1);
      const key = `${f.family}|${f.style}`;
      if (!seen.has(key)) { seen.add(key); fontsToLoad.push(f); }
    }
  }
  for (const f of fontsToLoad) {
    const key = `${f.family}|${f.style}`;
    if (!loadedFonts.has(key)) {
      await figma.loadFontAsync(f);
      loadedFonts.add(key);
    }
  }

  const variable = await figma.variables.getVariableByIdAsync(b.varId);
  node.setBoundVariable("characters", variable);
  mutatedNodeIds.push(b.nodeId);
}

return { mutatedNodeIds };
```

## Critical gotchas

### Gotcha #1: The font discovery API is `getRangeFontName`, not `getRangeAllFontNames`

`node.fontName` returns `figma.mixed` when a text node has multiple fonts (common with emoji or weight overrides). Calling `loadFontAsync(figma.mixed)` throws.

**Wrong**:
```javascript
await figma.loadFontAsync(node.fontName);  // throws on mixed-font nodes
node.setBoundVariable("characters", variable);
```

**Right**:
```javascript
// Walk character-by-character, collect unique fonts, load each
const seen = new Set();
const fonts = [];
for (let i = 0; i < node.characters.length; i++) {
  const f = node.getRangeFontName(i, i + 1);
  const key = `${f.family}|${f.style}`;
  if (!seen.has(key)) { seen.add(key); fonts.push(f); }
}
for (const f of fonts) await figma.loadFontAsync(f);
node.setBoundVariable("characters", variable);
```

For zero-length text nodes (rare but possible during construction), fall back to `node.fontName` directly.

### Gotcha #2: Text inside component instances cannot be bound directly

If the text node lives inside an `INSTANCE` (e.g. a Button's label, a Text Field's label), `setBoundVariable("characters", variable)` on the inner node **fails silently** — the binding doesn't persist because instance children are read-only proxies.

**Three resolution paths**, in order of preference:

1. **Modify the source component** — go to the main `COMPONENT` / `COMPONENT_SET` definition and bind the variable on the source text node. Every instance inherits the binding. This is the **system-ready** answer.

2. **Expose a TEXT component property + bind override per instance** — if the component already has a `TEXT` component property (e.g. Button's `label`), set the property's value via `instance.setProperties({ label: "..." })`. Then bind the **property** to the variable via the inner text node's bound-variable map on the source component. Variables can drive component properties on instances.

3. **Detach the instance** — last resort. Only acceptable for one-off marketing screens, never for systematic localization. Detachment breaks future component updates.

**Detection script** — before binding, check if the target is inside an instance:

```javascript
function isInsideInstance(node) {
  let n = node.parent;
  while (n) {
    if (n.type === "INSTANCE") return n;
    n = n.parent;
  }
  return null;
}

const node = await figma.getNodeByIdAsync(targetId);
const parentInstance = isInsideInstance(node);
if (parentInstance) {
  return { error: "INSIDE_INSTANCE", instanceId: parentInstance.id, hint: "Bind on source component instead" };
}
```

### Gotcha #3: `ALL_SCOPES` is the default — always override

`createVariable` defaults to `["ALL_SCOPES"]`, which makes your string appear in the color picker, the radius picker, the gap picker, every picker. This is a UX disaster for designers.

**Always set** `v.scopes = ["TEXT_CONTENT"]` immediately after creation.

### Gotcha #4: Variable mode resets to default on duplicated frames

When a designer duplicates a frame and the parent's variable mode wasn't explicitly set, the duplicate falls back to the collection's default mode (EN). If you need to author non-EN designs, set the mode explicitly on the frame:

```javascript
frame.setExplicitVariableModeForCollection(collection, modeId_TL);
```

Without this, switching the page mode to TL works, but a designer copying a single frame into a different page loses the override.

### Gotcha #5: Plugin API cannot publish the library

Same rule as tokens — after adding new spiels, the user must click **Publish Library** in the Figma UI. Until then, consumer files don't see the new variables. **Always remind the user to publish after a spiels update.**

## Switching languages (designer workflow)

1. Select any frame, section, or page
2. Right-click → **Variable mode → WAIS / Strings → TL / CB**
3. Or set the mode at the page level to swap everything at once

Set the mode at the **page level** for canonical multilingual previews (one page per language). Set at the **frame level** for side-by-side comparison decks.

## Engineering handoff — i18n integration

WAIS spiels map 1:1 to a flat JSON structure for `next-intl` or `i18next`:

```typescript
// messages/en.json
{
  "login": {
    "title": "Log in",
    "subtitle": "Ready to get back to it? Log in to continue your journey.",
    "help-link": "Need help logging in?",
    "submit-button": "Log in",
    "mobile-label": "Mobile Number",
    "passcode-label": "Passcode"
  },
  "shared": {
    "auth": { "welcome-back": "Welcome Back" }
  }
}
```

```tsx
// app/[locale]/login/page.tsx
import { useTranslations } from 'next-intl';

export default function LoginPage() {
  const t = useTranslations('login');
  return (
    <>
      <h1>{t('title')}</h1>
      <p>{t('subtitle')}</p>
      <a href="/help">{t('help-link')}</a>
    </>
  );
}
```

### Export script (proposed for future automation)

A `tools/wais-spiels-export.js` snippet can dump the collection to per-language JSON files matching the structure above. Run after every spiels update to keep `messages/*.json` in sync with Figma.

## Known gaps (no support in Figma variables)

| Need | Workaround | Reality |
|---|---|---|
| Pluralization (`{count, plural, =0 {no items} one {# item} other {# items}}`) | Author 2+ string variants (`cart/items-zero`, `cart/items-one`, `cart/items-many`) and switch in code | Figma string variables are static; ICU MessageFormat is code-only |
| Interpolation (`Hello, {name}`) | Use placeholder syntax (`Hello, {name}`) in the string value, resolve in code | Designers should know `{x}` tokens are runtime, won't render in preview |
| Rich formatting (bold spans, links inline) | Author plain text; apply formatting structurally in code | Variables drive `characters`, not styled runs |
| Right-to-left languages | None yet — flag as future-work if/when Arabic / Hebrew are added | Mirroring is a layout problem, not a string problem |
| Locale-specific dates / currency | Use `Intl.DateTimeFormat` / `Intl.NumberFormat` in code | Out of scope for string variables |

Mark code-only strings (interpolated, pluralized, formatted) with a `code-only/` prefix in WAIS so designers know not to expect a Figma preview:

```
code-only/cart/items-count        // "{count} items in cart"
code-only/transfer/success-toast  // "Sent ₱{amount} to {name}"
```

## Honest binding hierarchy

When deciding how to localize a piece of copy:

1. **Direct text node** (most common) → bind via `setBoundVariable("characters", variable)`. Works on title, subtitle, help links, any text the designer placed directly.
2. **Text inside an instance** → modify the source component first, then re-publish. Don't try to bind on the instance.
3. **Text driven by a component property** → set the property value to a variable reference. Cleanest for systemic copy like button labels.
4. **Text from a data source (API, user input)** → don't try to localize in Figma. Mock it with a representative variable for preview, then let runtime handle it.

## Quick reference — adding a new screen's spiels

1. Inspect the screen, list every user-facing string
2. Decide per string: `<screen>/<element>` (screen-only) or `shared/<context>/<element>` (cross-screen)
3. Run Step 2 (batch-create) with all strings + EN/TL/CB values
4. Run Step 3 (bindings) with `{ nodeId, varId }` pairs
5. Verify with a screenshot at each mode
6. Remind the user to **Publish Library**
7. If new shared strings were added, prompt: "Want me to refactor existing screens to use the shared variants?"
