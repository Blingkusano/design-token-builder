# Reference: Figma Variable Structure (Part 2 / Phase 7)

Load this file in full before creating any Figma variable collection in Phase 7. It defines the **only** collection structure to use when transferring tokens into a Figma file — always exactly three collections, named exactly as below. Don't create additional collections, don't rename these, don't split them further.

---
## The three collections

| Collection | Modes | Holds | Publishing |
|---|---|---|---|
| `Global` | single mode ("Value") | Every Tier 1 primitive, from every family, grouped internally by "/" name-prefixes | **Hidden from publishing** — see "Publishing visibility" below |
| `Color` | single mode ("Value") | The full `color.semantic` structure (background/content/border/overlay/focus_ring) — same taxonomy as `color.md`, unchanged | Published normally |
| `Foundation` | single mode ("Value") | Every Tier 2 semantic scale except color: spacing, radius, border, typography | Published normally |

If a file already has collections from an older structure (e.g. separate `Color / Primitives`, `Color / Semantic`, `Typography`, `Spacing & Radius` collections), this structure **supersedes** them — consolidate into the 3 collections above rather than leaving both sets side by side.

## 1. Collection `Global`

Single mode, named `Value`. Every variable name is grouped with a `/`-prefixed path so Figma's variable picker nests them:

* `color/[palette]/[palette]-[step]` — one group per palette **the user actually supplied**: `primary`, `neutral`, `success`, `warning`, `error`, `info` always, plus `secondary` and/or `accent` **only when the user gave one** (see `SKILL.md` Phase 2b's optional-palette rule — a *ไม่มี / None* answer means that group doesn't exist here at all, and no colour is invented to fill it). 11 variables per present palette, plus `color/white`, `color/black`, `color/black-alpha/black-alpha-[N]`, `color/white-alpha/white-alpha-[N]`. Type `COLOR`.
* `typography/font-family/font-family-[slug]` — type `STRING`.
* `typography/font-size/font-size-[N]` — type `FLOAT`.
* `typography/line-height/line-height-[N]` — type `FLOAT`.
* `typography/font-weight/font-weight-[N]` — type `FLOAT`. **Keys stay numeric** (`font-weight-400`, `font-weight-700`) — the `default`/`strong` ladder from `typography.md` is a Tier 2 vocabulary and never appears in `Global`.
* `typography/letter-spacing/letter-spacing-[...]` — type `FLOAT`.
* `spacing/space-[N]` — type `FLOAT`. 17 variables: `0, 2, 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64, 80, 96, 112, 128`.
* `radius/radius-[N]` — type `FLOAT`. **12 variables: `0, 2, 4, 8, 10, 12, 16, 20, 24, 32, 40, 999`.** There is no `radius/radius-full` variable any more — the pill step is `radius/radius-999`. If an older run of this skill created `radius/radius-full`, rename it to `radius/radius-999` (renaming keeps the variable ID, so existing bindings survive) and reset its value to `999`.
* `border/border-[...]` — type `FLOAT`. Four variables, values `0.5`, `1`, `2`, `4`. **The hairline's Figma variable is named `border/border-0-5`, not `border/border-0.5`** — Figma rejects a `.` anywhere in a variable name and `createVariable` throws `invalid variable name`. This is the one place the Figma name deliberately diverges from the token key, which keeps its documented literal decimal (`border.global.border-0.5`). `Foundation` `border/sm` therefore aliases `border/border-0-5`.

This is a direct, literal-value transcription of every `*.global` branch in `color.md`, `typography.md`, and `spacing-radius-elevation-borderwidth-focusring.md` — no aliasing happens inside `Global`, only inside `Color` and `Foundation`, which alias back into it.

### Publishing visibility

Every variable inside `Global` — all of it, every group, no exceptions — must be marked **Hidden from publishing** before this file is published as a library. `Global` exists only so `Color` and `Foundation` have raw values to alias into; it's an internal implementation detail, not something a consuming file should ever bind to directly. Hiding it keeps a published library's variable picker showing only the semantic `Color`/`Foundation` tokens, so downstream files can't accidentally bind to a raw primitive instead of a semantic one.

* **How:** in the Figma UI, select all variables in `Global` (or the whole collection) and toggle the "Hide from publishing" (eye-slash) option from the right-click / publish-review menu. Via the Plugin API, set `hiddenFromPublishing = true` on every `Variable` in the collection.
* **Scope:** this applies to `Global` only. `Color` and `Foundation` stay published/visible as normal — they're the intended public surface.
* **What it doesn't do:** hiding from publishing doesn't delete, rename, or change the value of anything — `Color` and `Foundation` still alias `Global` internally exactly as before within this file. It only controls what a *different* file sees after subscribing to this one as a library.
* **When:** do this once the `Global` collection's variables exist, before (or as part of) the first publish of the file — re-check it any time new `Global` variables are added later, since new variables default to published/visible and won't inherit the hidden state automatically.
* **Not affected by this rule:** the "Global Colors" documentation table on the `  --> Color` Foundation page (per `figma-page-structure.md`) still shows every `Global` primitive's name and hex directly — that's an in-file canvas table, not a published variable binding, so it's unrelated to this rule and needs no change.

## 2. Collection `Color`

Single mode, named `Value` — the same as `Global` and `Foundation`. There is no `Light`/`Dark` mode pair and no custom-theme mode; don't add a second mode. Holds the same `background`/`content`/`border`/`overlay`/`focus_ring` elements and role/emphasis keys as `color.md` Tier 2 — **up to 80 variables** (background 32, content 28, border 15, overlay 1, focus_ring 4). Every variable aliases a `Global` color variable; nothing here holds a literal.

**Optional palettes:** the count drops exactly as `color.md`'s conditional-roles table says. No Secondary → drop the five `background/brand_secondary-*` and four `content/brand_secondary-*` variables (**71 total**); no Accent → drop the four `content/accent-*` variables; neither → **67 total**. Don't create a variable whose `Global` alias target doesn't exist, and don't point a would-be `brand_secondary`/`accent` variable at the `primary` ramp to keep the count round.

**`background` follows `color.md`'s current direction, not the old one:** `background/[role]-primary` is the **lightest** step of its family and `secondary`/`tertiary`/`quaternary` step progressively deeper; `background/[role]-solid` is the single dark, contrast-bearing step. Never rebuild `background` with `primary` as the strong step.

**`background/base-plain`** is a flat variable (no emphasis) that aliases `Global` `color/white`.

### Values mirror the token JSON exactly

Every `Color` variable aliases **the same `Global` step that the token JSON's matching `color.semantic` key aliases** — read the step off the JSON; don't recompute it, re-search it, or adjust it. The HTML preview renders those same JSON values, so this is what keeps the Figma file identical to the preview the user approved. There is **no Figma-only value override**: the old 400–600 clamp is gone, and the "not too light, not too dark" range it provided now lives in `color.md` itself — `content`/`focus_ring` stay in `400`–`700`, and the `background.*-solid` search is bounded to `400`–`700`.

If a contrast problem shows up against a fill inside the Figma file, fix it by picking a different `content/*` token (e.g. `content/base-primary` instead of `content/white` on that fill), as the hand-off notes say — never by re-pointing a `Color` variable away from the step the JSON names.

Name the collection exactly `Color` — not `Color / Semantic`, not `Semantic Color`, no suffix.

### Variable scope per element

Set each `Color` variable's Figma **Scopes** property per its element, so the variable picker only offers a variable where it's actually meant to be used:

| Element | Applies to (Figma node/property) |
|---|---|
| `background` | Frame fill, Shape fill (`FRAME_FILL`, `SHAPE_FILL`) |
| `content` | Text fill, Shape fill (`TEXT_FILL`, `SHAPE_FILL`) |
| `border` | Stroke (`STROKE_COLOR`) |
| `focus_ring` | Stroke (`STROKE_COLOR`) |
| `overlay` | Frame fill, Shape fill (`FRAME_FILL`, `SHAPE_FILL`) |

Set scopes per-variable (or per-group, if the Figma UI allows bulk-selecting a whole element's variables at once) when each variable is created in Phase 7 — don't leave any `Color` variable on the default "All scopes."

## 3. Collection `Foundation`

Single mode, named `Value`. Holds the Tier 2 semantic scale for every family except color:

* `spacing/[name]` — **12 variables** (`none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, `7xl`), each aliasing its `Global` `spacing/space-[N]` counterpart per `spacing-radius-elevation-borderwidth-focusring.md`. **`spacing/md` aliases `spacing/space-8`** — verify this one explicitly, it moved from `space-16`.
* `radius/[name]` — **12 variables** (`none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, plus `full`), each aliasing its `Global` `radius/radius-[N]` counterpart. **`radius/md` aliases `radius/radius-8`**; `radius/full` aliases `radius/radius-999`.
* `border/[name]` — 4 variables (`sm`/`md`/`lg`/`xl`), each aliasing its `Global` `border/border-[...]` counterpart. Unchanged — `border/md` is still `border-1` (1px).
* `typography/[semantic-name]/font-family`, `.../font-size`, `.../line-height`, `.../font-weight`, `.../letter-spacing` — a Figma Variable can only hold one scalar, so each `typography.semantic` token becomes a **group of 5 alias variables**, one per property, each aliasing the matching `Global` `typography/*` primitive. **The group name uses the semantic weight ladder**, e.g. `typography/headline-xs-default/*`, `typography/body-md-strong/*` — never `-regular`/`-bold`/`-700`. On a default run that's 22 groups (`headline`×6 + `body`×5, × `default`/`strong`) = 110 variables.

Figma **Text Styles** are still the actual thing designers apply to text — build one per `typography.semantic` token in Phase 7b's Typography page (bind `fontSize` to the `Foundation` variable, or directly to `Global`, either is fine since they hold the same value). **Name each Text Style with the semantic ladder name too** (`headline-xs-default`, `body-lg-strong`), matching the variable group. The `Foundation` typography variables exist so the semantic-level values are also available as bindable variables, not just baked into Text Styles.

## Scope mapping for every variable family

Set Figma Scopes per family when each variable is created (the `Color` per-element table above is the colour row of this mapping):

| Structural Token | Targeted Property Node | Required Figma Scope |
| :--- | :--- | :--- |
| **SPACING / RADIUS** | Container Layout Properties | `GAP` (gap, padding), `CORNER_RADIUS` |
| **FILL COLOR** | Background Surfaces | `FRAME_FILL`, `SHAPE_FILL` |
| **STROKE COLOR** | Border Structural Contours | `STROKE_COLOR` |
| **TEXT COLOR** | Text Node Fills | `TEXT_FILL`, `SHAPE_FILL` (never `TEXT_CONTENT` — see `reference/figma-variable-structure.md`) |
| **EFFECT COLOR** | Shadow Effects | `EFFECT_COLOR` |
| **DIMENSIONS** | Spatial Bounds | `WIDTH_HEIGHT` |
| **TYPOGRAPHY** | Font Families, Sizes, Line Heights | `FONT_FAMILY`, `FONT_SIZE`, `LINE_HEIGHT` *(bundle into composite Text Styles)* |

### ⚠️ APPENDIX B: Common Pitfalls & Corrective Strategies
* **Pitfall 1:** Overlooking Explicit Variable Scoping. **Fix:** restrict visibility (e.g. text tokens only for text panels).
* **Pitfall 2:** Writing scale values straight into a `*.semantic` tier instead of aliasing `*.global`. **Fix:** generate Tier 1 first, then alias it — never the reverse.
* **Pitfall 3:** Copying actual values (hex codes, font sizes, spacing numbers) from a layout-reference Figma file into this one. **Fix:** `reference/figma-page-structure.md`'s layout reference is for structure only — every value on every page must trace back to this skill's own `Global`/`Color`/`Foundation` variables.

**Common pitfalls:** leaving a variable on the default "All scopes" (restrict it per the table above); copying hex codes, font sizes or spacing numbers from a layout-reference Figma file — `figma-page-structure.md`'s layout reference is for structure only, and every value on every page must trace back to this skill's own `Global` / `Color` / `Foundation` variables.

## Plugin API constraints (measured — not in Figma's own docs)

These bite during Phase 7 and each one costs a failed call plus a recovery call if you meet it cold. Write code that already obeys them.

* **No `.` in a variable name.** `figma.variables.createVariable('border/border-0.5', …)` throws `Error: in createVariable: invalid variable name`, and the throw rolls back the **whole** script — every variable created earlier in that same call is lost. Use `border/border-0-5` (see the `Global` `border/` bullet above). `/` is the group separator and is fine; `-` is fine.
* **`TEXT_CONTENT` is not a valid scope for a `COLOR` variable.** It scopes `STRING` variables bound to a text node's characters. Text colour is `TEXT_FILL`. Setting `TEXT_CONTENT` on a colour variable is rejected.
* **Create variables in bulk, not in small batches.** A loop that creates 100+ variables is one logical operation, not 100 — `figma-use`'s "≤10 operations per call" guidance is about distinct node-building steps. Building each collection in a single call (Global colours, Global scales, Color, Foundation) is both faster and far cheaper than splitting it.
* **Don't paste resolved values that already exist as variables.** When a Foundation page needs to print a hex next to a swatch, read it back with `figma.variables.getLocalVariablesAsync()` and resolve the alias chain inside the script, rather than pasting the palette into the script a second time. The palette is ~3k tokens; pasting it into every page script that needs it is pure waste.

---
## Migration note for files built under an older structure

If `Color / Primitives`, `Color / Semantic`, `Typography`, or `Spacing & Radius` collections already exist (from a run before this file existed): rebuild fresh `Global`/`Color`/`Foundation` collections per the rules above, rebind every existing node (swatches, text styles, spacing/radius/border demo bindings) to the new variable IDs, then delete the superseded collections. Renaming a variable in place keeps its ID (no rebinding needed); moving a variable's *collection* membership isn't supported by the Plugin API, so anything that needs to end up in a different collection than it started in has to be recreated and rebound.

Specifically for a file built by 1.0.0 (or an earlier v08/v09 run): `radius/radius-full` → rename to `radius/radius-999` (value `999`); `Foundation` `radius/*` drops from 13 to 12 variables; `spacing/md` and `radius/md` both need repointing to the `8` primitive; every `typography/*-regular` group renames to `*-default` and every `*-bold` group to `*-strong`; add `background/base-plain`; add the four `content/brand_secondary-*` variables.

**For a file built by 1.2.0 or earlier** (two-mode `Color` collection): don't delete or rewrite the existing `Dark` mode — removing a mode is destructive and can't be undone from the Plugin API side. Write the new values into the first mode, rename that mode to `Value`, leave any other mode untouched, and list it as a deviation in the Phase 7 report so the user decides whether to delete it.

## Self-check before finishing Phase 7
* Exactly three collections exist: `Global`, `Color`, `Foundation` — no others left over — and each has exactly one mode, named `Value`.
* Every `Color` variable aliases a `Global` variable; every `Foundation` variable aliases a `Global` variable; nothing in `Color`/`Foundation` holds a literal value.
* `Color`'s variable count matches the project's palette configuration — 80 with both optional palettes present (background 32 including `base-plain` and the seven `-solid` states, content 28, border 15, overlay 1, focus_ring 4), less the conditional roles otherwise.
* `Global` has a `color/*` group for every supplied palette and **none** for a palette answered *None* — and no `Color` variable references a missing group.
* Every `Color` variable aliases exactly the `Global` step the token JSON names for that key — zero differences between the Figma file and the JSON / HTML preview.
* `background/[role]-primary` is the lightest step of its family.
* `content/brand-secondary`, `content/brand_secondary-secondary`, `content/accent-secondary` all resolve to step `700`, as in the JSON.
* `Foundation` `spacing/md` and `radius/md` both resolve to **8**; `radius/full` resolves to `999`; no `radius/radius-full` variable remains anywhere.
* `Foundation` `spacing/*` has 12 variables and `radius/*` has 12 variables.
* Every `Foundation` `typography/*` group and every Text Style is named with the semantic ladder (`-default`/`-strong`/…), and no `Global` `font-weight` variable was renamed away from its numeric key.
* Every `Color` variable's Scopes are set per the element table above — no variable left on the default "All scopes."
* Every variable in `Global` is set to **Hidden from publishing**; every variable in `Color` and `Foundation` remains published/visible.
* The hairline variable is named `border/border-0-5` (no `.`), and `Foundation` `border/sm` resolves to `0.5` through it.
* No `Color` variable carries the `TEXT_CONTENT` scope — `content/*` uses `TEXT_FILL` + `SHAPE_FILL`.
