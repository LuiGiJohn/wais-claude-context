# wais-tokens

**Version**: v0.7 (post-publish 2026-05-14)
**WAIS source**: `qzjPZX4jHQTgRoUgkySmWT`

Complete reference for DT WAIS variables, text styles, and effect styles. Use this skill before binding any color, text, or shadow in WAIS or any WAIS consumer file.

## Variable collections (3)

### 1. Primitives (77 variables, 1 mode "Value")
Raw values — never bind directly in production. Always alias through Tokens or Semantic.
- `Color/*` (51): the full color palette
- `Scale/*` (19): spacing/sizing primitives
- `Typography/*` (7): font primitives

### 2. Tokens (125 variables, 1 mode "Value")
Numeric/sizing tokens with semantic naming.
- `Border-Radius/*` (7)
- `Border-Width/*` (4)
- `Color/*` (84) — named color tokens (e.g. `Color/Amber/50`)
- `Typography/*` (30)

### 3. Semantic (83 variables, 1 mode "Light")
Use these for production bindings. **Description coverage: 100%.**
- `Surface/*` (25): backgrounds
- `Text/*` (29): text colors
- `Icon/*` (17): icon vector fills
- `Border/*` (12): borders/strokes

## Critical token keys for active use

### Surface tokens
| Token | Key | Resolves to | Use |
|---|---|---|---|
| `Surface/Page` | `735c60249d2d10d64b8d21e1c9e3d2238b92f141` | white | screen bgs |
| `Surface/Brand-Subtle` | `57fb7de286dc05e047f158657720a786d0789477` | Primary/50 | subtle cyan tints |
| `Surface/Brand-Feature` ⭐ NEW v0.7 | `6a2e57deba5b11f28ec50eb4f16cc829fb3cab36` | Teal/200 (#A9EFF8) | Quick Action buttons, glass action elements |
| `Surface/Brand-Neutral-Light` ⭐ NEW v0.7 | `b40e26c3fdfc5404fe0e53ed52581a7a88e882b1` | Teal/100 (#D1F8FC) | "Go for More" bubble, subtle cyan accents |
| `Surface/Brand-Primary` | `637dc7e36bbd2b54a1537e1024947a0a2f673fc2` | Teal/500 | primary cyan elements |
| `Surface/Warning/Default` | `e1ee17ef14376e300a11ce5bbc940d904b098776` | Amber/500 (#F9AA16) | Icon Button Tertiary bg, warning amber |

### Text tokens
| Token | Key | Use |
|---|---|---|
| `Text/Default` | `2a22716cc0c6de129ba7f211c952c33d898c4a16` | primary content |
| `Text/Secondary` | `9ec798ab8c97cc2cb0e140235591312d2f7db529` | supporting copy, subtitles |
| `Text/Body/Default` | `77fde5d02d1e7e70d6e879f3c0a3336770cb58ce` | body labels, card captions |
| `Text/Brand-Primary` | `03f310bd0137967a7161f5850889f88f7effe217` | brand-colored text |
| `Text/Action/Link` | `b2455b0afe4760adc169eda2342f349679f7dc14` | hyperlinks, account numbers |
| `Text/Action/On-Action` | `5cc18b57c49ab3fdbb0c08e4856d86293b035913` | white text on action backgrounds |
| `Text/Action/On-White` | (lookup) | text on white when an action color is the base |
| `Text/Warning/On-Warning` | `4d57177232889b5cfb93ebf2849bd2ade7ec7478` | text on warning amber bgs |
| `Text/Display` | (lookup) | bento card display titles |

### Icon tokens
| Token | Key | Use |
|---|---|---|
| `Icon/Default/Default` | `0561e3db297fd4cf4389ead30387a332f55cbeea` | default icon vector fill |
| `Icon/Brand-Primary` | `5de7e0cd969e049786cf570c5d561e170c647dfa` | Teal/800 — Quick Action icons, action bubble icons |
| `Icon/Action/Default` | `ab90a60253df0bfa9e6d093484147ea231dc60d6` | icons on action surfaces |
| `Icon/Warning/On-Warning` | `fbd19e2936fcde796ad7368025c5e83bc88ba8c9` | amber icons on amber bgs (back/close buttons) |

### Border tokens
| Token | Key | Use |
|---|---|---|
| `Border/Default` | `33ed15e7bc8316dcc0e0d8ed14aa6549c5f21cdf` | default 1px stroke |

### Raw color tokens (still legitimate where no semantic exists)
| Token | Key | Hex |
|---|---|---|
| `Color/Amber/50` | `2a5ac5f298ca04070b98aece7441594d6b7ce74d` | #FFFBEB |
| `Color/Amber/100` | `019e9c670b2dfd1fc984c704cae925c6d451c838` | #FFF5C6 |
| `Color/Amber/400` | `7f12caae1a25e8b4...` | #FEC521 |
| `Color/Amber/500` | `6871503c5e2068f402b676b6ce9751b9ab7a68e0` | #F9AA16 |
| `Color/Amber/700` | `79940243df92c198...` | darker amber |
| `Color/Teal/100` | `e103d2e18fd9e1981cbf394a231a144bbddfa5fe` | #D1F8FC |
| `Color/Teal/200` | `a136e11b1a3b8b039a299ae43e9e8fb0e0236d3f` | #A9EFF8 |
| `Color/Neutral/80` | `ecfab74fb00b7b6be35f8d5b803dfd0b3544cefd` | mid neutral |
| `Color/Neutral/95` | `2ccd919e052a821e7c5f1b37ea3f4d7b99a43c69` | light neutral |
| `Color/Primary/50` | `38f08c171322836e02cf7e5ea74d425163be60ed` | #EDFDFE |

## Text styles (19)

### Title family (5)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Title/Screen` | Bold 34/41 / -1% | `409c95701a9e5053b2cf9031c3708a17817ae448` | page h1 |
| `Title/Section` | SemiBold 28/34 / -0.28px | `45934df35c1ffb95307eb2e358a55925986a5e7f` | major section headers, hero amounts |
| `Title/Subsection` | SemiBold 22/28 / -0.5px | `c386bfd09e9cefa280aa013b3d9863e85874832f` | sub-section headers ("Account Settings") |
| `Title/Display` | SemiBold 26/32 / -0.26px | `65c85525a0e4...` | display-oriented titles |
| `Title/Group` | SemiBold 18/24 / -0.18px | `5b0d810b2879ead8f25ea0a16e842cc70b66e713` | grouped amounts, sub-hero figures |

### Body family (6)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Body/Large` | Regular 20/30 | `f1da99e8af7f357d0bd6c8470cdf6c245a8f79c1` | large body text |
| `Body/Default` | Medium 16/24 / -0.5px | `2cb0a4e8c8cfabe0009f1cc63b050ebd022ad6eb` | default body |
| `Body/Regular` | Regular 16/24 | `19cf9eeff289917d892b4ae83ce41a718d99ddcf` | regular body |
| `Body/Small` | Regular 14/22 | `0c00d77ed1fef6c2ebddf5782ac9e3cf04be0671` | smaller body |
| `Body/Small-Medium` | Medium 14/22 | `2a5f7a34f29bcadace47c2104c5ad0dd14f9f0f3` | medium-weight small body |
| `Body/Small-SemiBold` | SemiBold 14/20 | `483b4477a60b0bf7eb65d9d7ef3f877b8e3b1095` | semibold small body |

### Display family (1)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Display/Banner` ⭐ NEW v0.7 | ExtraBold 14 / 110% lh / +1.5% ls | `8e3f252ac204fdc99a1230bf7b5e178f5fe7083b` | bento card banner titles (uppercase) |

### Caption family (3)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Caption/Default` | Medium 12/16 | `1715d53f4b881b7badafd42d2a7e2b2ee96b3d2a` | card labels, captions |
| `Caption/Strong` | Medium 11/14 | `a23ee94e7bb2...` | strong caption variant |
| `Caption/Tiny` | Medium 10/12 | `39b22a897bb310744c9a0f31d57b92904b24a52a` | smallest captions (Hot Offer chip) |

### Button family (2)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Button/Label-Small` | SemiBold 14/20 / +0.17px | `372da0657bfc3f3742e00ccd323ad56a1b42005c` | small button labels, pill labels |
| `Button/Label-Default` | SemiBold 16/24 / +0.5px | `336716bcc1438caa05dc8f97fd5a9076ae70930d` | default button labels |

### Link family (2)
| Style | Spec | Key | Use |
|---|---|---|---|
| `Link/Default` | SemiBold 16/24 | `fe15c75254bd...` | inline links |
| `Link/Large` | SemiBold 16/24 / +1px | `99bae693da5b...` | prominent link text |

## Effect styles (3)
| Style | Key | Use |
|---|---|---|
| `Elevation/Low` | `76564b1cdaba...` | subtle card lift |
| `Elevation/Medium` | `abdb224c2723...` | floating panels |
| `Elevation/High` | `8037be988437...` | modals, popovers |

## Known gaps (no token / style exists for these)

| Need | Workaround | Future WAIS addition |
|---|---|---|
| Bold body 14/22 | bind Body/Default + override `fontName` to SemiBold inline | add `Body/Default-Bold` style |
| Bold body 16/24 | bind Body/Default + inline weight override | add `Body/Large-Bold` style |
| Neutral icon-button bg (#DBE4E7) | inline rgb on Tertiary fallback | add `Surface/Icon-Button-Neutral` token |
| Mid-jump Icon Button size (slot 24-28) | use Size=Default (slot 20) or Size=Large (slot 32) | add `Size=Medium` if needed |
| Modal overlay rgba(10,18,21, 0.56) | inline color w/ opacity | add `Surface/Overlay` token w/ alpha |

## Patterns for proper binding

```javascript
// Color variable binding
function bindFill(node, variable) {
  const modeId = Object.keys(variable.valuesByMode)[0];
  const val = variable.valuesByMode[modeId];
  const fallback = (val && val.r !== undefined) ? val : { r: 0, g: 0, b: 0 };
  node.fills = [figma.variables.setBoundVariableForPaint(
    { type: "SOLID", color: fallback }, "color", variable
  )];
}

// Text style binding (note: async)
await textNode.setTextStyleIdAsync(style.id);

// Then bind color separately
bindFill(textNode, tokens.textDefault);
```

## Honest binding hierarchy

When deciding which token to use:
1. **Prefer Semantic over Token over Primitive** — always
2. **Text colors**: Text/Default (primary), Text/Secondary (supporting), Text/Body/Default (labels/captions), Text/Action/Link (links)
3. **Surface colors**: Surface/Page (white screens), Surface/Brand-* (themed cards), Surface/Warning/Default (amber accents)
4. **Icon colors**: Match the surface — Icon/Brand-Primary on Brand-Feature, Icon/Warning/On-Warning on Warning, Icon/Default on neutral
