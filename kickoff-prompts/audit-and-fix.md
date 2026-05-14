# Audit and fix kickoff

User wants to audit the DT-Onboarding-Screens consumer file (or WAIS source) for drift and remediate findings.

## Required skill loads

- `skills/wais-tokens.md`
- `skills/wais-components.md`
- `skills/wais-screens.md`
- `skills/wais-figma-gotchas.md`

## Audit workflow

1. **Scope confirmation** — ask:
   - Audit WAIS source, consumer file, or both?
   - Specific page(s) or all?
   - Just report findings, or also remediate?

2. **Run bulk audit script** via `use_figma` — walks all screens, identifies:
   - Unstyled text nodes (`!node.textStyleId`)
   - Hardcoded fills (`fill.type === "SOLID" && !fill.boundVariables?.color`)
   - Hardcoded strokes (same pattern)
   - Component instances pointing to detached or remote-only main components
   - Layer order / scroll pattern issues
   - Skip instance internals (those are WAIS's responsibility, not consumer's)

3. **Categorize findings**:
   - **Category A**: Legitimate inline content (logotypes, placeholder fills like camera/preview bgs, semitransparent overlays without semantic equivalent)
   - **Category B**: Genuine cleanup (recent work missing bindings, recently-added styles not yet applied)
   - **Category C**: Pre-existing drift (older screens that predate current conventions)

4. **Present findings** as `yaml` summary:
   ```yaml
   audit_results:
     total_screens: N
     clean: N
     with_issues: N
     total_issues: N
     by_category:
       A_acceptable: [...]
       B_fixable_now: [...]
       C_legacy_drift: [...]
   ```

5. **Propose remediation** — ask which categories to fix:
   - Always: Category B
   - Often: Category C (with caveats — older work)
   - Never: Category A (documented as accepted exceptions)

6. **Execute fixes** in batched `use_figma` calls. Render-verify after each batch.

7. **Re-audit** to confirm zero drift remains.

8. **Final delivery summary** with:
   - Audit stats before/after
   - List of fixes applied
   - Remaining honest scope notes (anything intentionally left)

## Audit script template (use as base)

```javascript
const log = [];
const summary = { totalScreens: 0, totalIssues: 0, byScreen: {} };

for (const page of figma.root.children) {
  await page.loadAsync();
  if (["Cover", "--"].includes(page.name)) continue;
  
  const screens = page.findAll(n => 
    n.type === "FRAME" && 
    Math.round(n.width) === 402 && 
    Math.abs(n.height - 874) < 50
  );
  
  for (const screen of screens) {
    summary.totalScreens++;
    let issues = [];
    
    function audit(node) {
      if (node.type === "INSTANCE") return;
      
      if (node.type === "TEXT" && !node.textStyleId && node.parent?.type !== "INSTANCE") {
        issues.push(`Unstyled text: "${(node.characters || "").substring(0, 30)}"`);
      }
      
      if ((node.type === "FRAME" || node.type === "RECTANGLE") && Array.isArray(node.fills)) {
        for (const f of node.fills) {
          if (f.type === "SOLID" && !f.boundVariables?.color) {
            const rgb = `${Math.round(f.color.r*255)},${Math.round(f.color.g*255)},${Math.round(f.color.b*255)}`;
            if (rgb !== "255,255,255" && rgb !== "0,0,0") {
              issues.push(`Hardcoded fill ${rgb} on "${node.name}"`);
            }
          }
        }
      }
      
      // ... (stroke check, etc. — see wais-screens.md)
      
      if ("children" in node && node.children) {
        for (const c of node.children) audit(c);
      }
    }
    
    audit(screen);
    summary.totalIssues += issues.length;
    if (issues.length > 0) summary.byScreen[screen.name] = issues;
  }
}

return { summary };
```

## Communication style

- Skip apologies. Direct.
- Findings in `yaml` blocks for scannability.
- Always categorize before fixing.
- Honest scope notes — flag what you're NOT fixing and why.
- After fix, run drift checker if available to confirm clean state.
