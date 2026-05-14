# wais-screens

**Version**: v2.0 (session 2026-05-14)

Recipes and patterns for building consumer screens using WAIS components and tokens.

## Standard screen frame

402×874 mobile frame. Bind bg to `Surface/Page`. `clipsContent = true`. `overflowDirection = "VERTICAL"` if content scrolls, otherwise `"NONE"`.

Chrome elements (Status Bar, Bottom Nav, Home Indicator) live at the END of the children list with `numberOfFixedChildren` set to count of chrome elements.

```javascript
const screen = figma.createFrame();
screen.name = "Profile / Default";
screen.resize(402, 874);
bindFill(screen, tokens.surfacePage);
screen.clipsContent = true;
// ... build content first ...
// ... then append chrome at end ...
screen.numberOfFixedChildren = 2;  // Status Bar + Home Indicator
screen.overflowDirection = "NONE";
```

## Section organization

Group related screens horizontally in a SECTION node on the Dashboard Flow page:
- Section width = 80 padding + N × 402 + (N-1) × 80 gap + 80 padding
- Section height = 80 + 874 + 80 = 1036
- Each screen at section-relative position (80 + i × (402 + 80), 80) for i = 0..N-1

```javascript
// 5-screen section: 80 + 5×402 + 4×80 + 80 = 2490 wide
section.resizeWithoutConstraints(2490, 1036);
```

## Status Bar choice

- `Style=Light` (`177684b4cc794af2c31751a8ec4c5a552141336a`) — for screens with image bgs, dark headers, gradient bgs
- `Style=Dark` (`2fb71ec4d74c5f9bab4cabe100d28e35cc88d474`) — for screens with white/light bgs (Profile, Total Earnings, settings, etc.)

## Back / close button convention (v0.7+)

**Always** use Icon Button Appearance=Tertiary, Type=Filled, Size=Default for back/close.

```javascript
const iconBtnTertiary = await figma.importComponentByKeyAsync(
  "cf5b8955256c9e65d66e9f46b0a1f64ef359a02e"
);

// Inside Controls bar:
const btn = iconBtnTertiary.createInstance();  // native 44×44, amber bg + amber icon
controls.appendChild(btn);

// Swap inner icon to the right glyph
const inner = btn.findOne(n => n.type === "INSTANCE");
inner.swapComponent(arrowTailLeft);  // for back
// OR: inner.swapComponent(closeIcon); // for close
inner.name = "arrow_back";  // or "close"

// REQUIRED: re-bind vectors (swapComponent resets them) — see Gotcha #21
bindIconVectors(inner, tokens.iconWarning);  // Icon/Warning/On-Warning
```

## Controls bar pattern

Top app bar at y=62, h=44, full-width 402:

```javascript
const controls = figma.createFrame();
controls.name = "Controls";
controls.layoutMode = "HORIZONTAL";
controls.primaryAxisSizingMode = "FIXED";
controls.counterAxisSizingMode = "AUTO";
controls.primaryAxisAlignItems = "SPACE_BETWEEN";  // or START for back-only
controls.counterAxisAlignItems = "CENTER";
controls.paddingLeft = 16;
controls.paddingRight = 16;
controls.fills = [];
controls.resize(402, 44);
screen.appendChild(controls);
controls.x = 0;
controls.y = 62;

// LEFT: back/close button
// RIGHT: optional trust pill, hidden spacer, or action icon
```

## Pill button pattern (Icon+Text)

Used in Profile for "Regular Savings Account":

```javascript
const pill = figma.createFrame();
pill.name = "Button - Icon+Text";
pill.layoutMode = "HORIZONTAL";
pill.primaryAxisSizingMode = "AUTO";  // hug width
pill.counterAxisSizingMode = "FIXED";
pill.itemSpacing = 4;
pill.primaryAxisAlignItems = "CENTER";
pill.counterAxisAlignItems = "CENTER";
pill.paddingLeft = 14;
pill.paddingRight = 14;
pill.paddingTop = 8;
pill.paddingBottom = 8;
pill.resize(180, 34);  // initial, will hug
pill.cornerRadius = 1000;  // full rounded
bindFill(pill, tokens.surfaceBrandFeature);

// Icon (24×24, bound to Icon/Brand-Primary)
const verifiedInst = verifiedIcon.createInstance();
verifiedInst.resize(24, 24);
bindIconVectors(verifiedInst, tokens.iconBrandPrimary);
pill.appendChild(verifiedInst);

// Label (Button/Label-Small style, Text/Brand-Primary color)
const label = figma.createText();
await label.setTextStyleIdAsync(buttonLabelSmall.id);
label.characters = "Regular Savings Account";
bindFill(label, tokens.textBrandPrimary);
pill.appendChild(label);
```

## List item pattern (settings/profile)

```javascript
const listItem = figma.createFrame();
listItem.name = "List";
listItem.layoutMode = "HORIZONTAL";
listItem.primaryAxisSizingMode = "FIXED";
listItem.counterAxisSizingMode = "AUTO";
listItem.primaryAxisAlignItems = "SPACE_BETWEEN";
listItem.counterAxisAlignItems = "CENTER";
listItem.paddingTop = 16;
listItem.paddingBottom = 16;
listItem.fills = [];
listItem.clipsContent = true;
listItem.resize(354, 56);
listGroup.appendChild(listItem);

// Left: icon + label
const leftFrame = figma.createFrame();
leftFrame.name = "Frame";
leftFrame.layoutMode = "HORIZONTAL";
leftFrame.primaryAxisSizingMode = "AUTO";
leftFrame.counterAxisSizingMode = "AUTO";
leftFrame.itemSpacing = 12;
leftFrame.counterAxisAlignItems = "CENTER";
leftFrame.fills = [];
listItem.appendChild(leftFrame);

const icon = settingsIcon.createInstance();
icon.resize(24, 24);
bindIconVectors(icon, tokens.iconDefault);
leftFrame.appendChild(icon);

const label = figma.createText();
await label.setTextStyleIdAsync(bodyDefault.id);  // ⚠️ Body/Default, NOT Body/Large
label.characters = "Settings";
bindFill(label, tokens.textDefault);
leftFrame.appendChild(label);

// Right: chevron
const chevron = arrowNoTailRight.createInstance();
chevron.resize(24, 24);
bindIconVectors(chevron, tokens.iconDefault);
listItem.appendChild(chevron);
```

## Avatar pattern (bordered amber, e.g. Profile "MC")

```javascript
const avatar = figma.createFrame();
avatar.name = "Avatar";
avatar.layoutMode = "HORIZONTAL";
avatar.primaryAxisAlignItems = "CENTER";
avatar.counterAxisAlignItems = "CENTER";
avatar.resize(56, 56);
avatar.cornerRadius = 296;  // fully rounded
bindFill(avatar, tokens.amber100);
bindStroke(avatar, tokens.amber500);
avatar.strokeWeight = 2;
avatar.strokeAlign = "INSIDE";
avatar.clipsContent = true;

// Inner 36×36 icon slot
const avatarIcon = figma.createFrame();
avatarIcon.name = "Icon";
avatarIcon.layoutMode = "VERTICAL";
avatarIcon.primaryAxisAlignItems = "CENTER";
avatarIcon.counterAxisAlignItems = "CENTER";
avatarIcon.resize(36, 36);
avatarIcon.fills = [];
avatarIcon.cornerRadius = 100;
avatar.appendChild(avatarIcon);

// Initials text
const avatarText = figma.createText();
await avatarText.setTextStyleIdAsync(titleSection.id);  // SemiBold 28/34
avatarText.characters = "MC";
bindFill(avatarText, tokens.amber500);
avatarText.textAlignHorizontal = "CENTER";
avatarText.textAlignVertical = "CENTER";
avatarIcon.appendChild(avatarText);
```

## Quick Action button row (post-v0.7)

```javascript
const qaRow = figma.createFrame();
qaRow.name = "Quick Actions";
qaRow.layoutMode = "HORIZONTAL";
qaRow.primaryAxisSizingMode = "FIXED";
qaRow.counterAxisSizingMode = "AUTO";  // HUG vertically — important for proper sizing
qaRow.primaryAxisAlignItems = "SPACE_BETWEEN";
qaRow.paddingLeft = 8;
qaRow.paddingRight = 8;
qaRow.clipsContent = false;  // ← labels need room to render
qaRow.resize(370, 90);

for (const action of ["Transfer", "Bills", "Load", "Remittance"]) {
  const item = figma.createFrame();
  item.layoutMode = "VERTICAL";
  item.counterAxisSizingMode = "FIXED";  // fixed 80 width
  item.primaryAxisSizingMode = "AUTO";    // HUG vertically
  item.counterAxisAlignItems = "CENTER";
  item.paddingTop = 5;
  item.paddingBottom = 5;
  item.itemSpacing = 8;
  item.clipsContent = false;
  item.resize(80, 90);
  qaRow.appendChild(item);
  
  // Icon Button Subtle Filled Large (56×56, 32×32 slot)
  const btn = iconBtnSubtleFilledLarge.createInstance();
  bindFill(btn, tokens.surfaceBrandFeature);  // override default Subtle bg
  
  // Swap inner icon to Size=32 variant
  const innerIcon = btn.findOne(n => n.type === "INSTANCE");
  innerIcon.swapComponent(actionIcon32);
  innerIcon.resize(32, 32);
  bindIconVectors(innerIcon, tokens.iconBrandPrimary);
  
  item.appendChild(btn);
  
  // Caption/Default label
  const label = figma.createText();
  await label.setTextStyleIdAsync(captionDefault.id);
  label.characters = action;
  bindFill(label, tokens.textDefault);
  item.appendChild(label);
}
```

## Text hierarchy guide

| Use case | Style | Token |
|---|---|---|
| Page h1 ("Total Earnings", "Profile") | Title/Screen | Text/Default |
| Hero amounts ("PHP 8,262.37") | Title/Section | Text/Default |
| Sub-hero amounts ("PHP 7,262.37", "PHP 1,000.00") | Title/Group | Text/Default |
| Section header ("Account Settings") | Title/Subsection | Text/Default |
| Card title ("Earned Interest", "Total Earnings" caption) | Caption/Default | Text/Body/Default |
| Body text in cards (Tagalog descriptions) | Body/Default | Text/Secondary |
| Subtitle ("Ang total gain mo!") | Body/Default | Text/Secondary |
| List item labels | Body/Default | Text/Default |
| Footer/legal text | Caption/Default | Text/Secondary |
| Account number link | Title/Group | Text/Action/Link |
| Pill button label | Button/Label-Small | Text/Brand-Primary |
| Bento banner title (uppercase) | Display/Banner | Text/Display |
| Hot Offer chip text | Caption/Tiny | Color/Amber/700 |

## Modal / overlay pattern

For Tips welcome modal or any overlay screen:
- Background: 100% width/height clone of underlying screen (with chrome stripped)
- Overlay: full-width semitransparent rgba(10, 18, 21, 0.56) on top
- Pop-up card: rounded corners, drop shadow, contains content
- Chrome (Status Bar, Home Indicator) goes at TOP of stack (visible above overlay)

For tooltip with "cutout" highlighting an element:
- Bottom Nav goes BEHIND overlay (it's part of the dimmed underlay)
- Overlay has top/bottom strips with a transparent middle "window"
- Tip Pop card + Tail point at the visible element
- Status Bar + Home Indicator at top of stack

Layer order is set by children order in the parent frame's children array — first child renders at back, last renders at front.

## Render-verify checklist after building

1. Call `figma_get_component_image` on the new screen
2. Verify visually that:
   - Layout looks aligned with reference
   - Colors render properly (no purple "missing variable" indicators)
   - Text wraps and sizes correctly
   - No overflow / clipped content
   - Chrome elements (Status Bar, Home Indicator) sit at correct z-order
3. Run binding audit — walk all descendants and confirm:
   - All FRAMEs and RECTANGLEs with non-white/black fills have bound colors
   - All TEXT nodes have a `textStyleId` (except inside WAIS instances)
   - All component instances point to local-WAIS main components
