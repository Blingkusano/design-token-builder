# Reference: Spacing, Radius, Elevation, Border, Focus ring

Load this file in full before writing JSON for any of these five branches. Each is its own top-level (or, for `focus_ring`, nested-under-`color.semantic`) branch — they're only grouped in this file for documentation convenience, per explicit user request.

---
## 1. Spacing — `spacing.global` / `spacing.semantic`

**Tier 1 — `spacing.global`:** raw scale `space-0` through `space-128`, `$type: "dimension"`, key = the raw px number (e.g. `space-16` = `"16px"`). Generate on a 4px grid: `0, 2, 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64, 80, 96, 112, 128` (add/remove steps only if the user's spacing grid explicitly differs from 4px).

**Tier 2 — `spacing.semantic`:** anchored T-shirt scale, 12 steps, **raw `16px` = `md`**, named outward in both directions:
`none, 3xs, 2xs, xs, sm, md(=16px), lg, xl, 2xl, 3xl, 4xl, 5xl`. Each `$value` aliases the matching `spacing.global.*` primitive — never a literal. **`none` is the smallest step and aliases `spacing.global.space-0` (0px)** — it replaces what an outward T-shirt count would otherwise name `4xs`, because a step that resolves to zero reads better as "no spacing" than as another size increment. Step count is unchanged (still 12); only that one label differs from a strict T-shirt progression.

## 2. Radius — `radius.global` / `radius.semantic`

**Tier 1 — `radius.global`:** raw scale `radius-0` through `radius-full`, `$type: "dimension"`. Include a `radius-full` primitive (`9999px`) for pill/circular shapes alongside the numeric steps.

**Tier 2 — `radius.semantic`:** anchored T-shirt scale, **raw `8px` = `md`**, 12 numeric steps named outward (`none` through `6xl`) **plus one extra unanchored step `full`** (aliases `radius.global.radius-full`) for pills/avatars — `full` sits outside the outward-anchored numbering, not at either end of it. Each `$value` aliases `radius.global.*`. **`none` is the smallest of the 12 numeric steps and aliases `radius.global.radius-0` (0px)** — same reasoning and rule as `spacing.semantic.none` above: it replaces what would otherwise be named `3xs`, step count unchanged.

## 3. Border — `border.global` / `border.semantic`

**Tier 1 — `border.global`:** raw scale `border-0.5` (0.5px, hairline), `border-1`, `border-2`, `border-4`. **`border-0.5` keeps its literal decimal value in the key** instead of a word like `half` — a deliberate one-off exception to the round-to-integer decimal-encoding rule used elsewhere (`typography.global.line-height-[N]`, `letter-spacing-[N]`): this family only has four steps total, so a literal `0.5` reads unambiguously and doesn't need multiplying out.

**Tier 2 — `border.semantic`:** anchored 4-step scale, **raw `1px` = `md`**: `sm` (=`border-0.5`, 0.5px), `md` (=`border-1`, 1px), `lg` (=`border-2`, 2px), `xl` (=`border-4`, 4px). Each `$value` aliases `border.global.*`.

## 4. Elevation — `elevation.global` / `elevation.semantic`

**Tier 1 — `elevation.global`:** raw shadow primitives `shadow-none`, `shadow-sm`, `shadow-md`, `shadow-lg`, `$type: "shadow"` (or `$type: "boxShadow"` per whatever DTCG shadow type the rest of the file set uses), `$value` an offset/blur/spread/color object (color aliasing `color.global.black-alpha-*`).

**Tier 2 — `elevation.semantic`:** exactly 4 keys, **renamed from the old numeric `0/1/2/3` scheme** — `none`, `sm`, `md`, `lg` — each aliasing the matching `elevation.global.shadow-*` primitive 1:1. Do not add intermediate steps; do not revert to numeric keys.

**JSON location:** `elevation` stays its own top-level branch, sibling to `color`/`spacing`/`radius` — never folds into `color.semantic.[theme].shadow` (see `color.md`'s scoping note).

## 5. Focus ring — `color.semantic.[theme].focus_ring`

Documented here per the user's grouping preference, but it is **not** its own top-level branch — in the actual JSON it nests under `color.semantic.[theme].focus_ring`, exactly like `background`/`content`/`border`/`overlay` (see `color.md` Tier 2).

**Role list (closed set):** `brand-primary`, `brand-secondary`, `error-primary`, `error-secondary` — 4 tokens, no `emphasis`/`state` suffixing beyond what's already baked into the role name.

**Value-mapping:** follows `color.md`'s Tier 2 value-mapping convention — `brand-*` roles alias the `primary` palette family, `error-*` roles alias the `error` palette family; `primary`/`secondary` within the role name pick the step the same way `emphasis` does elsewhere (primary = strongest/most-used step, typically matching that palette's accessible `content`/`border`-primary step; secondary = a lighter variant). Each `$value` aliases `color.global.*` — never a literal, never cross-referencing another `color.semantic` element.

## Locked JSON skeleton (this file's branches)
```jsonc
{
  "spacing":      { "global": {}, "semantic": {} },
  "radius":       { "global": {}, "semantic": {} },
  "border":       { "global": {}, "semantic": {} },
  "elevation":    { "global": {}, "semantic": { "none": {}, "sm": {}, "md": {}, "lg": {} } },
  "color": { "semantic": { "light": { "focus_ring": {
    "brand-primary": {}, "brand-secondary": {}, "error-primary": {}, "error-secondary": {}
  } } } }
}
```

## Self-check before output (run silently)
* `spacing.semantic` / `radius.semantic` / `border.semantic` each anchor their raw input value at `md`, name outward, and every `$value` aliases the matching `*.global.*` primitive.
* `spacing.semantic`'s smallest step is named `none` (not `4xs`) and aliases `spacing.global.space-0`; `radius.semantic`'s smallest numeric step is named `none` (not `3xs`) and aliases `radius.global.radius-0`.
* `radius.semantic` has the 12 anchored steps (starting at `none`) plus the separate unanchored `full` step (13 total).
* `border.global` keeps its hairline step as the literal `border-0.5`, not a word like `half`.
* `elevation.semantic` uses exactly `none`/`sm`/`md`/`lg` — no numeric keys, no extra steps.
* `color.semantic.[theme].focus_ring` has exactly the 4 roles listed, each aliasing `color.global.*`.
* Nothing in this file's five branches contains a literal (non-alias) value at the semantic tier — except `border.global.border-0.5`'s own key name, which is the one documented literal-in-key exception.
