# Reference: Spacing, Radius, Elevation, Border, Focus ring

Load this file in full before writing JSON for any of these five branches. Each is its own top-level (or, for `focus_ring`, nested-under-`color.semantic`) branch — they're only grouped in this file for documentation convenience, per explicit user request.

---
## 1. Spacing — `spacing.global` / `spacing.semantic`

**Tier 1 — `spacing.global`:** raw scale `space-0` through `space-128`, `$type: "dimension"`, key = the raw px number (e.g. `space-16` = `"16px"`). Generate exactly this ladder: `0, 2, 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64, 80, 96, 112, 128` (17 steps; add/remove steps only if the user's spacing grid explicitly differs).

**Tier 2 — `spacing.semantic`:** anchored T-shirt scale, 12 steps, **raw `8px` = `md`** (changed from `16px` — `md` is the 8px step now, and every other label shifts with it). Named outward in both directions from that anchor:

| Semantic step | Global primitive | px |
|---|---|---|
| `none` | `spacing.global.space-0` | 0 |
| `xs` | `spacing.global.space-2` | 2 |
| `sm` | `spacing.global.space-4` | 4 |
| **`md`** | **`spacing.global.space-8`** | **8** |
| `lg` | `spacing.global.space-12` | 12 |
| `xl` | `spacing.global.space-16` | 16 |
| `2xl` | `spacing.global.space-20` | 20 |
| `3xl` | `spacing.global.space-24` | 24 |
| `4xl` | `spacing.global.space-32` | 32 |
| `5xl` | `spacing.global.space-40` | 40 |
| `6xl` | `spacing.global.space-48` | 48 |
| `7xl` | `spacing.global.space-64` | 64 |

Each `$value` aliases the matching `spacing.global.*` primitive — never a literal. **`none` is the smallest step and aliases `spacing.global.space-0` (0px)** — it replaces what an outward T-shirt count would otherwise name `3xs`, because a step that resolves to zero reads better as "no spacing" than as another size increment. Step count is unchanged (still 12); only the anchor moved and the labels shifted with it. The `spacing.global` steps not picked up by a semantic label (`56`, `80`, `96`, `112`, `128`) stay available as Tier 1 primitives for layout-level use — that's expected, not a gap.

## 2. Radius — `radius.global` / `radius.semantic`

**Tier 1 — `radius.global`:** raw scale, `$type: "dimension"`, key = the raw px number. Generate exactly this ladder — **12 steps, locked**: `0, 2, 4, 8, 10, 12, 16, 20, 24, 32, 40, 999`. `radius-999` (= `"999px"`) is the pill/circular step; it replaces the older `radius-full` (`9999px`) primitive — use the numeric `radius-999` key, not the word `full`, at Tier 1. The word `full` survives only as the Tier 2 semantic name.

**Tier 2 — `radius.semantic`:** anchored T-shirt scale, **raw `8px` = `md`**, 11 numeric steps named outward **plus one extra unanchored step `full`** (aliases `radius.global.radius-999`) for pills/avatars — `full` sits outside the outward-anchored numbering, not at either end of it. 12 semantic tokens total.

| Semantic step | Global primitive | px |
|---|---|---|
| `none` | `radius.global.radius-0` | 0 |
| `xs` | `radius.global.radius-2` | 2 |
| `sm` | `radius.global.radius-4` | 4 |
| **`md`** | **`radius.global.radius-8`** | **8** |
| `lg` | `radius.global.radius-10` | 10 |
| `xl` | `radius.global.radius-12` | 12 |
| `2xl` | `radius.global.radius-16` | 16 |
| `3xl` | `radius.global.radius-20` | 20 |
| `4xl` | `radius.global.radius-24` | 24 |
| `5xl` | `radius.global.radius-32` | 32 |
| `6xl` | `radius.global.radius-40` | 40 |
| `full` | `radius.global.radius-999` | 999 |

Each `$value` aliases `radius.global.*` — never a literal. **`none` is the smallest of the 11 numeric steps and aliases `radius.global.radius-0` (0px)** — same reasoning and rule as `spacing.semantic.none` above.

**Note on the two ladders:** `spacing.global` and `radius.global` are deliberately *different* scales now — spacing keeps its full 17-step layout ladder up to `128`, radius uses the tighter 12-step ladder ending in the `999` pill step. Don't merge them or generate one from the other. What they share is the anchor: both semantic scales put `md` at `8px`.

## 3. Border — `border.global` / `border.semantic`

**Tier 1 — `border.global`:** raw scale `border-0.5` (0.5px, hairline), `border-1`, `border-2`, `border-4`. **`border-0.5` keeps its literal decimal value in the key** instead of a word like `half` — a deliberate one-off exception to the round-to-integer decimal-encoding rule used elsewhere (`typography.global.line-height-[N]`, `letter-spacing-[N]`): this family only has four steps total, so a literal `0.5` reads unambiguously and doesn't need multiplying out. **`border` is deliberately excluded from the spacing/radius numeric ladders** — a stroke scale needs the sub-pixel hairline and the `1px` default that those ladders don't carry.

**Tier 2 — `border.semantic`:** anchored 4-step scale, **raw `1px` = `md`**: `sm` (=`border-0.5`, 0.5px), `md` (=`border-1`, 1px), `lg` (=`border-2`, 2px), `xl` (=`border-4`, 4px). Each `$value` aliases `border.global.*`. Note this `md` stays at `1px` — the "md = 8" rule applies to `spacing` and `radius` only.

## 4. Elevation — `elevation.global` / `elevation.semantic`

**Tier 1 — `elevation.global`:** raw shadow primitives `shadow-none`, `shadow-sm`, `shadow-md`, `shadow-lg`, `$type: "shadow"` (or `$type: "boxShadow"` per whatever DTCG shadow type the rest of the file set uses), `$value` an offset/blur/spread/color object (color aliasing `color.global.black-alpha-*`).

**Tier 2 — `elevation.semantic`:** exactly 4 keys, **renamed from the old numeric `0/1/2/3` scheme** — `none`, `sm`, `md`, `lg` — each aliasing the matching `elevation.global.shadow-*` primitive 1:1. Do not add intermediate steps; do not revert to numeric keys.

**JSON location:** `elevation` stays its own top-level branch, sibling to `color`/`spacing`/`radius` — never folds into `color.semantic.[theme].shadow` (see `color.md`'s scoping note).

## 5. Focus ring — `color.semantic.[theme].focus_ring`

Documented here per the user's grouping preference, but it is **not** its own top-level branch — in the actual JSON it nests under `color.semantic.[theme].focus_ring`, exactly like `background`/`content`/`border`/`overlay` (see `color.md` Tier 2).

**Role list (closed set):** `brand-primary`, `brand-secondary`, `error-primary`, `error-secondary` — 4 tokens, no `emphasis`/`state` suffixing beyond what's already baked into the role name.

**Value-mapping:** `brand-*` roles alias the `primary` palette family, `error-*` roles alias the `error` palette family. Both stay inside the same **`400`–`700` band** `color.md` locks for `content`:
* **`brand-primary` = step `600`, `brand-secondary` = step `700`** — fixed, matching `color.md`'s fixed-step exception for `content.brand-*` so a focus ring and its brand text agree. Don't run a contrast search on these two.
* `error-primary` / `error-secondary` follow the contrast-driven rule inside `400`–`700`: `primary` is the most-accessible step against the surface it rings (3:1 minimum per `SKILL.md` Phase 3), `secondary` a lighter variant.

Each `$value` aliases `color.global.*` — never a literal, never cross-referencing another `color.semantic` element.

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
* `spacing.semantic.md` and `radius.semantic.md` both resolve to **8px** — verify literally; this is the single most common regression.
* `spacing.semantic` has exactly the 12 steps `none…7xl` from the table above; `radius.semantic` has exactly the 11 numeric steps `none…6xl` plus `full` (12 total).
* `radius.global` is exactly `0, 2, 4, 8, 10, 12, 16, 20, 24, 32, 40, 999` — no `radius-full` primitive survives, no extra steps.
* `spacing.global` is the 17-step ladder up to `128` and was **not** replaced by the radius ladder.
* Every `spacing.semantic.*` / `radius.semantic.*` / `border.semantic.*` `$value` aliases the matching `*.global.*` primitive — zero literals at Tier 2.
* `border.semantic.md` is still `1px`, and `border.global` keeps its hairline step as the literal `border-0.5`, not a word like `half`.
* `elevation.semantic` uses exactly `none`/`sm`/`md`/`lg` — no numeric keys, no extra steps.
* `color.semantic.[theme].focus_ring` has exactly the 4 roles listed, each aliasing `color.global.*` inside the `400`–`700` band, with `brand-primary`=`600` and `brand-secondary`=`700`.
* Nothing in this file's five branches contains a literal (non-alias) value at the semantic tier — except `border.global.border-0.5`'s own key name, which is the one documented literal-in-key exception.
