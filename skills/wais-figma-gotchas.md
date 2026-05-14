# wais-figma-gotchas

**Version**: v2.0 (session 2026-05-14)

Hard-won lessons working with the WAIS design system in Figma. Read this BEFORE writing `use_figma` code.

## Critical universals

### Gotcha #1 — Async API everywhere
- `page.loadAsync()` mandatory before reading children of any page that isn't `figma.currentPage`
- Font loading mandatory before ANY text op (creation, edit, style binding, swapComponent on instances with text)
- Required fonts for WAIS: Work Sans (Bold, SemiBold, Medium, Regular, ExtraBold) + Inter (Regular, Medium, Semi Bold, Bold)

```javascript
await Promise.all([
  figma.loadFontAsync({ family: "Work Sans", style: "Bold" }),
  figma.loadFontAsync({ family: "Work Sans", style: "SemiBold" }),
  figma.loadFontAsync({ family: "Work Sans", style: "Medium" }),
  figma.loadFontAsync({ family: "Work Sans", style: "Regular" }),
  figma.loadFontAsync({ family: "Work Sans", style: "ExtraBold" }),
  figma.loadFontAsync({ family: "Inter", style: "Regular" }),
  figma.loadFontAsync({ family: "Inter", style: "Medium" }),
  figma.loadFontAsync({ family: "Inter", style: "Semi Bold" }),
  figma.loadFontAsync({ family: "Inter", style: "Bold" }),
]);
```

**Note**: Inter style is `"Semi Bold"` (with space), not `"SemiBold"`. Same for `"Extra Bold"` vs `"ExtraBold"`.

### Gotcha #2 — No async helpers inside use_figma
Top-level await only. Inline everything. Don't define `async function` helpers — they break execution.

### Gotcha #3 — figma.currentPage assignment
Setting `figma.currentPage = page` is not supported. Use `await figma.setCurrentPageAsync(page)` instead.

### Gotcha #4 — Library publishing is UI-only
Plugin API cannot publish a library. Designer must click "Publish Library" in Figma UI. After publish:
- Consumer files can import new content via `importComponentByKeyAsync` / `importStyleByKeyAsync` / `variables.importVariableByKeyAsync`
- Existing instances in consumer files do NOT auto-refresh layout (see Gotcha #20)

### Gotcha #5 — Plugin data API
- `getPluginData` / `setPluginData` are web-only and need manifest IDs
- Use `getSharedPluginData(namespace, key)` / `setSharedPluginData(namespace, key, value)` instead
- Pick a stable namespace (≥3 chars, alphanumeric/_/.)

## Component & variant gotchas

### Gotcha #6 — Remote components can't be renamed or reparented
A component imported from a library is read-only in terms of name + parent. To "localize":
1. `importComponentByKeyAsync(key)` to get the main component
2. `mainComponent.createInstance()` to make an instance
3. `instance.detachInstance()` to detach
4. `figma.createComponentFromNode(detachedFrame)` to create a new local component
5. Optionally `figma.combineAsVariants([components])` to merge into a set

### Gotcha #7 — Never .remove() source rectangles during component creation
When wrapping a node in a component, use `node.parent = newComponent` (or `appendChild`) to MOVE the node, not `node.remove()` + recreate.

### Gotcha #8 — Adding new variants to a set
To add Size=XL or similar to an existing component_set:
1. `existingVariant.clone()` to duplicate a similar variant
2. Rename to reflect new axis values (`Size=Large` → `Size=XL`)
3. Resize / modify as needed
4. `componentSet.appendChild(newVariant)` — Figma auto-extends the variant axis

To remove variants: `variant.remove()`. The axis updates automatically.

## Layout & coordinate gotchas

### Gotcha #15 — SECTION.appendChild reinterprets x/y as section-relative
```javascript
page.appendChild(screen); screen.x = 80; screen.y = 80;  // page coords
section.appendChild(screen);                               // x/y now treated as section-relative
// To compensate, re-assign:
screen.x = 80; screen.y = 80;  // section-relative
```

### Gotcha #16 — Screen scroll pattern
- Screen frame: fixed `width/height` + `clipsContent = true`
- Chrome (Status Bar, Bottom Nav, Home Indicator) at END of children list
- `numberOfFixedChildren` counts from the END — set to count of chrome elements
- `overflowDirection = "VERTICAL"` when content overflows, `"NONE"` otherwise
- `scrollBehavior` property does NOT exist in current Plugin API

### Gotcha #18 — Auto-layout pages reorder by child index, not y-coord
The Changelog page (744:2) has VERTICAL auto-layout. Setting `child.y` won't stick — auto-layout overrides it. Use `parent.insertChild(index, node)` to control position.

For newest-first ordering on Changelog: insert new version at index 1 (after Hero at index 0).

### Gotcha #22 — layoutSizingHorizontal requires specific conditions
`node.layoutSizingHorizontal = "FILL"` fails with "node must be an auto-layout frame or a child of an auto-layout frame" when:
- The parent isn't auto-layout
- The node isn't yet appended to a parent
- The parent's `counterAxisSizingMode` is `"AUTO"` (HUG) — FILL doesn't make sense there

When unsure, use explicit `node.resize(w, h)` instead of relying on layoutSizingHorizontal.

## Audit / Bind gotchas

### Gotcha #19 — Ad cards / promotional cards on dashboard are RECTANGLE, not FRAME
Generic audit filters like `node.type === "FRAME"` miss them. To catch all background shapes:
```javascript
const allShapes = node.findAll(n => 
  (n.type === "FRAME" || n.type === "RECTANGLE") && 
  Array.isArray(n.fills) && n.fills.length > 0
);
```

### Gotcha #20 — Instance layout caches until republish
When WAIS spec changes (e.g., Size=Large slot 28→32 in v0.7), consumer file instances still show OLD dimensions. To force refresh:
1. WAIS designer publishes (UI action)
2. Consumer file: `instance.swapComponent(sameComponent)` to re-sync layout
3. Re-apply any property overrides that may have reset

### Gotcha #21 — swapComponent resets vector fill bindings ⭐ NEW
After `innerIconInstance.swapComponent(newIcon)`, the vector fills inside the new instance carry their main component's default bindings (typically Icon/Default/Default), NOT the previous instance's overrides. **Always rebind vector fills after swap:**
```javascript
innerIcon.swapComponent(newIcon);
bindIconVectors(innerIcon, tokens.iconWarning);  // re-bind required
```

## Variable / Token gotchas

### Gotcha #11 — Variable binding requires fallback color
`figma.variables.setBoundVariableForPaint` needs a fallback paint. Read the variable's value via `valuesByMode[modeId]` first:
```javascript
const modeId = Object.keys(variable.valuesByMode)[0];
const val = variable.valuesByMode[modeId];
const fallback = (val && val.r !== undefined) ? val : { r: 0, g: 0, b: 0 };
node.fills = [figma.variables.setBoundVariableForPaint(
  { type: "SOLID", color: fallback }, "color", variable
)];
```

For ALIAS variables (Semantic → Tokens → Primitives), `valuesByMode` returns `{ type: "VARIABLE_ALIAS", id: "..." }` — won't have `r/g/b`. Use a sensible fallback color literal.

### Gotcha #12 — Style keys differ from variant keys
A text style's `key` is the short hash before the comma in `style.id`. The full `style.id` is like `S:8e3f252ac204fdc99a1230bf7b5e178f5fe7083b,` — strip the prefix and comma when noting the key elsewhere.

### Gotcha #13 — importComponentByKeyAsync fails on truncated keys
Always copy the FULL component/style key (40 hex chars). Truncated keys produce "not found" errors. Copy from a freshly-fetched inventory, not from memory.

## Honest scope reminders

- **Always render-verify after any visual change** via `figma_get_component_image` or `figma_capture_screenshot`
- **Audit instances skip their internals** — bindings inside a WAIS instance are the WAIS component's responsibility. Only audit OVERRIDES at the instance level.
- **Hardcoded #FFFFFF and #000000 are usually acceptable** (no semantic token for "pure white screen" or "pure black overlay" — these are intentional)
- **Cloned dashboards in Tips screens go stale** when the source dashboard changes. Refresh by re-cloning if you've made downstream changes.

## Quick command reference

```javascript
// Find a component_set by name
const set = figma.root.findOne(n => n.type === "COMPONENT_SET" && n.name === "Icon Button");

// Get all variants
const variants = set.children.filter(c => c.type === "COMPONENT");

// Find a specific variant
const tertiary = set.children.find(c => 
  c.name === "Appearance=Tertiary, Type=Filled, Size=Default, State=Default"
);

// Audit a node tree, skipping instances
function audit(node, callback) {
  if (node.type === "INSTANCE") return;
  callback(node);
  if ("children" in node && node.children) {
    for (const c of node.children) audit(c, callback);
  }
}

// Bind a fill to a variable
function bindFill(node, variable) {
  const modeId = Object.keys(variable.valuesByMode)[0];
  const val = variable.valuesByMode[modeId];
  const fallback = (val && val.r !== undefined) ? val : { r: 0, g: 0, b: 0 };
  node.fills = [figma.variables.setBoundVariableForPaint(
    { type: "SOLID", color: fallback }, "color", variable
  )];
}

// Recursively bind icon vectors
function bindIconVectors(node, variable) {
  if (node.type === "VECTOR" && node.fills?.length > 0 && node.name !== "Bounding box") {
    bindFill(node, variable);
  }
  if (node.type === "RECTANGLE" && node.name === "Bounding box") {
    node.fills = [];  // clear hit-target rect
  }
  if ("children" in node && node.children) {
    for (const c of node.children) bindIconVectors(c, variable);
  }
}
```
