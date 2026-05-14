# wais-components

**Version**: v0.7 (post-publish 2026-05-14)
**WAIS source**: `qzjPZX4jHQTgRoUgkySmWT`

Complete reference for DT WAIS components — what exists, where it lives, and how to instantiate.

## Component pages (15)

| Page | Sets | Notable components |
|---|---|---|
| Button | 3 | Button (144), Button - Pill (144), Icon Button (54) |
| Inputs | 1 | Text Field (4 variants) |
| Selection | 6 | Checkbox, Radio, *List Item variants, Toggle, Toggle List Item |
| Avatar | 1 | Avatar (9 variants) |
| Navigation | 3 | Top Nav (2), Bottom Nav (4), Tab Bar Item (2) |
| Dialog | 1 | Action (3) |
| Progress | 2 | Progress Tracker (5), Progress Indicator (4) |
| Numpad | 1 | Passcode (3) |
| List | 0 sets, 2 standalone |  |
| System UI | 2 sets, 3 standalone | Status Bar (2), Keyboard (2), Home Indicator |
| Spinner | 1 | Spinner (3) |
| Icons | 78 | full icon library |
| Illustrations | 20 | branded illustrations |
| Effects | 1 | Scroll Edge Effect (2) |
| Cover | 1 standalone | — |

## High-frequency components

### Button (144 variants)
Component set: `7:5` → `Button`
- Axes: Appearance (Primary, Secondary, Tertiary) × Type (Filled, Outlined) × Size (Small, Default, Large) × State (Default, Pressed, Disabled) × Has Icon × ...
- Square button variant Primary Filled Default key: `7efb7df909febf9dcea9bebe762c8aafc41d560f`
- Square Outlined Default (used for "Skip" buttons): `b183e79c366698e0b1dee037739a46e559417d45`

### Button - Pill (144 variants)
Component set: `7:5` → `Button - Pill`
- Pill-shaped variant of Button — same axes
- Primary Outlined Default Small: `0b6bf7d794a4bdc6c71c0c458ffd5994fb7446a1`
- Primary Outlined Default (large): `48fc2c60103a1cf497c5ab78f6e7fe9d88b6874f`

### Icon Button (54 variants) ⭐ UPDATED v0.7
Component set: `7:5` → `Icon Button`
- Axes: Appearance × Type × Size (Small/Default/Large) × State
- **Size axis spec (post-v0.7):**
  - `Size=Small`: 40×40 outer, 16×16 slot
  - `Size=Default`: 48×48 outer (44×44 hosted in some cases), 20×20 slot
  - `Size=Large`: 56×56 outer, **32×32 slot** ⭐ (was 28×28 pre-v0.7)
- Critical variant keys:
  - **Tertiary Filled Default** (back/close button pattern): `cf5b8955256c9e65d66e9f46b0a1f64ef359a02e`
  - Subtle Filled Large Default (Quick Actions): `3e237e9f6dbc88731086607f45d5a23b98074c9d`
  - Primary Outlined Default: `af91cd8d163c4132d46567ad780a8003008b2265`
  - Subtle Outlined Default: `38078f3c1329a94153fb6e3b522d56e5484fb0ec`

**Appearance → token mapping** (built into each variant):
| Appearance | bg token | inner icon token |
|---|---|---|
| Primary | Surface/Action/Default | Icon/On-Action/Default |
| Secondary | Surface/Action/Subtle | Icon/Action/Default |
| Subtle | Surface/Section | Icon/Default/Default |
| Tertiary | Surface/Warning/Default | Icon/Warning/On-Warning |

### Status Bar (2 variants)
| Variant | Key | Use |
|---|---|---|
| Style=Light | `177684b4cc794af2c31751a8ec4c5a552141336a` | screens with image/dark bg |
| Style=Dark | `2fb71ec4d74c5f9bab4cabe100d28e35cc88d474` | screens with white bg |

### Home Indicator
Single component, key: `9b12348f6bf149cf145dbec9cf746e6a4f2da6b4`

### Bottom Nav (4 variants)
Active Tab=Home: `da5e0cde30bfdc01af0db58e9f42723c94f406ed`
Component set: `4f015369aca9d1ae0fb52ce1498a353f9270a6ff`

### Top Nav (2 variants)
State=Default: `429579256fcc6d907651a134e8118a952edae9cf`
Component set: `d05421d7705ffa5bf2f94b8556848563d4e2b9cd`

## Common icon component keys

All Size=24 variants unless noted.

### Navigation icons
| Icon | Key |
|---|---|
| arrow_Tail (Direction=Left) | `6ba9303933f256d789a86baf00688e2a7ede4dd8` |
| arrow_Tail (Direction=Outward) | `0077f7c90577bc16...` |
| arrow_NoTail (Right) | `a16c96dacdbdbb6cdd4e0900cea1ae3e4ddcc041` |
| arrow_NoTail (Bottom) | `802da1f7c4d7d6750c4ac3daa4b2c13100acaa69` |
| close | `f3ac8147cdc5621307e13e1a169e2095406b97cb` |
| keyboard_double_arrow | (lookup as needed) |
| backspace | (lookup as needed) |

### Action icons (have Size=24 AND Size=32 variants)
| Icon | Size=24 | Size=32 |
|---|---|---|
| transfer | `1f95f4d072e0054314bc36f50514897602293633` | `096992309a4ab667c9e7a560d03da727b2bdd5d9` |
| bills | `9700aef81a5ccfa50268789c8b3822e3c7058a63` | `b58f73de52164be4e8bfbe5b7877f7bd6d6660c1` |
| load | `2b4a597808964cbcb0a3fd8fc59625d6620c80d2` | `466988c780d1573847d70850e5c7fbde18d333d6` |
| remittance | `04541fcd4bcd9cc30a482c7e238d084613c4e3a5` | `79de81ea18a7da53ab545593d14c5dda1cd6ad7a` |

### UI icons
| Icon | Key |
|---|---|
| content_copy | `5c3fe0aa4192ee41c986eef2b60907616158891e` |
| verified | `536cb9596d926dc72ed12f923dc00d3dc0605d8e` |
| photo_camera | `280c5cc4bafbe343c736b1eeee54e7655a1c1c1d` |
| calendar | `65c734cf842c40c0c7a324aac517ae3398f452c6` |

### Profile / Settings icons
| Icon | Key |
|---|---|
| dt_score | `a76b129d3e1b6369041905ff11f5c75efdac6aac` |
| invite | `76a9a2bb8151470b5c4f814189661232189e39ce` |
| settings | `939218d99b9d4e4a0390a226f2c8902c8cc5c60e` |
| help | `a2ab047b82edbdb8e3e723d894310c6803788044` |
| rate | `60a81f180629e95d637d3ae5763aaa50483b5102` |
| logout | `5f4c22895d63d7db391cdf560b0cf0dac9063a0a` |
| promotions | `ed058420b506c8083f905df26ef7ab64822c4ce0` |
| family_group | (lookup) |
| share | (lookup) |

## Illustration components (20)

All in `Page: Illustrations` → component definitions section.

**Animated** (most have Type=Animated variant):
| Illustration | Key |
|---|---|
| Sparkle | `49d24e64f89491ee865a03ca6775547e5d565a9c` |
| Alert | `d67b6d33...` |
| Complete | `9017d670...` |
| Gift Animated | `ce9d480e...` |
| ID Verification Animated | `660417c8...` |
| Network | `0ef2b370...` |
| Share | `4f1b463b...` |
| Upgrade | `b682f159...` |
| Waiting | `a943495e...` |
| Wallet | `282484c8...` |

**Static / Type=Animated bento icons**:
| Illustration | Type=Animated variant key |
|---|---|
| Gov | `0ecd0056234774eec3b0de287667b67445ab5d50` |
| Loan | `7ec3e878b6112a27b86301838a6d4517c1738bf6` |
| Gift | `e69af35f712e6e7075bd6ed3d4792dfc92ba451f` |
| badge | `3a5554754d4147f73528bcc9bfa4dedbc0dca990` |

Bento illustrations bind their bg fill to `Background/Brand/accent-light` (resolves to #FFF5C6 amber). They inherit this binding from WAIS — instances don't need bg override.

## Other components

### Text Field (4 variants)
State=Default: `1b9a1af6d69d880076dcb4b2bf734681ab24c888`

### Selection
- Radio List Item Unselected: `75add05331ccffa4c80953ca792bcb7554923dea`
- Radio List Item Selected: `e5e64498eb098438c84c70fd7a6f735d4962d61a`
- Toggle State=On Default: `d869a72f5fddab599723be75adf97e41db95b560`

### Progress Tracker
- Status=100%: `33986d2306d336f668852d4dcadd06bfcc05c116`
- Status=0: `edbc92cb0d1ad6093b1300a2a3ab718625115ac2`

### Spinner Size=Large
Key: `1d90107c10c91d7c152b036fb85ce32bbe80c4a0`

## Library publishing rules

1. Plugin API **cannot** publish a library — only manual UI action ("Publish Library" button)
2. After publishing, consumer files see new content via `importComponentByKeyAsync` / `importStyleByKeyAsync`
3. Instances of existing components cached in consumer files don't auto-update layout — see Gotcha #20 in `wais-figma-gotchas`

## Conventions for component use

**Back / close buttons on screens (NEW v0.7 convention)**:
Use Icon Button Appearance=Tertiary, Type=Filled, Size=Default at 44×44
- Native dimensions match — no resize needed
- bg → Surface/Warning/Default (amber)
- Inner icon → Icon/Warning/On-Warning (amber, must be rebound after swap)
- Swap the default Favorite icon to arrow_Tail Left (back) or close (dismiss)

**Quick Action button rows**:
Icon Button Subtle Filled Large (56×56, 32×32 slot post-v0.7)
- bg override to Surface/Brand-Feature
- Inner icon at Size=32, vectors bound to Icon/Brand-Primary

**Bento card icons (Gov, Loan, Gift)**:
Use the local component_set's Type=Animated variant. Inherits amber bg natively. Don't override.

**List items in settings/profile screens**:
Custom 354×56 frame with:
- paddingY=16, hstack space-between, counter=CENTER
- Leading: hstack gap=12 (icon 24×24 + Body/Default label)
- Trailing: arrow_NoTail Right (chevron 24×24)

**Avatar (bordered amber, e.g. Profile "MC")**:
56×56 frame, cornerRadius=296, fill Color/Amber/100, stroke Color/Amber/500 2px inside.
Inner 36×36 icon slot with Title/Section text bound to Color/Amber/500.
