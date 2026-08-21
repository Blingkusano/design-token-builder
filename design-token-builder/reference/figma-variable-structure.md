# Reference: Figma Variable Structure (Part 2 / Phase 7)

Load this file in full before creating any Figma variable collection in Phase 7. It defines the **only** collection structure to use when transferring tokens into a Figma file — always exactly three collections, named exactly as below. Don't create additional collections, don't rename these, don't split them further.

---
## The three collections

| Collection | Modes | Holds | Publishing |
|---|---|---|---|
| `Global` | single mode ("Value") | Every Tier 1 primitive, from every family, grouped internally by "/" name-prefixes | **Hidden from publishing** — see "Publishing visibility" below |
| `Color` | "Light", "Dark" | The full `color.semantic` structure (background/content/border/overlay/focus_ring) — same taxonomy as `color.md`, unchanged | Published normally |
| `Foundation` | single mode ("Value") | Every Tier 2 semantic scale except color: spacing, radius, border, typography | Published normally |

If a file already has collections from an older structure (e.g. separate `Color / Primitives`, `Color / Semantic`, `Typography`, `Spacing & Radius` collections), this structure **supersedes** them — consolidate into the 3 collections above rather than leaving both sets side by side.

## 1. Collection `Global`

Single mode, named `Value`. Every variable name is grouped with a `/`-prefixed path so Figma's variable picker nests them:

* `color/[palette]/[palette]-[step]` — one group per palette: `primary`, `secondary`, `neutral`, `accent`, `success`, `warning`, `error`, `info` (11 variables each), plus `color/white`, `color/black`, `color/black-alpha/black-alpha-[N]`, `color/white-alpha/white-alpha-[N]`. Type `COLOR`.
* `typography/font-family/font-family-[slug]` — type `STRING`.
* `typography/font-size/font-size-[N]` — type `FLOAT`.
* `typography/line-height/line-height-[N]` — type `FLOAT`.
* `typography/font-weight/font-weight-[N]` — type `FLOAT`.
* `typography/letter-spacing/letter-spacing-[...]` — type `FLOAT`.
* `spacing/space-[N]` — type `FLOAT`.
* `radius/radius-[N]` (plus `radius/radius-full`) — type `FLOAT`.
* `border/border-[...]` — type `FLOAT` (includes the literal-decimal hairline step, `border/border-0.5`).

This is a direct, literal-value transcription of every `*.global` branch in `color.md`, `typography.md`, and `spacing-radius-elevation-borderwidth-focusring.md` — no aliasing happens inside `Global`, only inside `Color` and `Foundation`, which alias back into it.

### Publishing visibility

Every variable inside `Global` — all of it, every group, no exceptions — must be marked **Hidden from publishing** before this file is published as a library. `Global` exists only so `Color` and `Foundation` have raw values to alias into; it's an internal implementation detail, not something a consuming file should ever bind to directly. Hiding it keeps a published library's variable picker showing only the semantic `Color`/`Foundation` tokens, so downstream files can't accidentally bind to a raw primitive instead of a semantic one.

* **How:** in the Figma UI, select all variables in `Global` (or the whole collection) and toggle the "Hide from publishing" (eye-slash) option from the right-click / publish-review menu. Via the Plugin API, set `hiddenFromPublishing = true` on every `Variable` in the collection.
* **Scope:** this applies to `Global` only. `Color` and `Foundation` stay published/visible as normal — they're the intended public surface.
* **What it doesn't do:** hiding from publishing doesn't delete, rename, or change the value of anything — `Color` and `Foundation` still alias `Global` internally exactly as before within this file. It only controls what a *different* file sees after subscribing to this one as a library.
* **When:** do this once the `Global` collection's variables exist, before (or as part of) the first publish of the file — re-check it any time new `Global` variables are added later, since new variables default to published/visible and won't inherit the hidden state automatically.
* **Not affected by this rule:** the "Global Colors" documentation table on the `  --> Color` Foundation page (per `figma-page-structure.md`) still shows every `Global` primitive's name and hex directly — that's an in-file canvas table, not a published variable binding, so it's unrelated to this rule and needs no change.

## 2. Collection `Color`

Two modes, `Light` and `Dark`. Holds the same `background`/`content`/`border`/`overlay`/`focus_ring` elements and role/emphasis/state keys as `color.md` Tier 2 — do not change that taxonomy, it's already correct. Every variable aliases a `Global` color variable.

**Figma-only value override for `-primary` roles:** for any semantic key whose role is sourced from a brand or status palette (i.e. `brand`, `brand_secondary`, `accent`, `error`, `warning`, `success`, `info` — **not** `base`/`white`/`placeholder`/`disabled`, which stay on the neutral layering logic already in `color.md`) **and** whose emphasis is `primary`, alias it to that palette's own anchor step (the exact step in `Global` that holds the user's original input hex for that palette — see `color.md` Tier 1's anchoring rule) **clamped into the 400–600 range**: if the anchor is below 400, use 400; if above 600, use 600; if it's already 400/500/600, use it as-is. This applies to `content.*-primary`, `border.*-primary`, and `focus_ring.*-primary` alike.

**`background` is excluded from this override.** Since `color.md`'s `background` change, `background.*-primary` is a light tint (as low as `100`, no numeric floor) — clamping it into 400–600 would fight that rule directly, so don't apply the clamp there. Instead, apply this same 400–600 clamp logic to **`background.*-solid`**: alias it to the palette's anchor step, clamped into 400–600, exactly like the other `-primary` roles above. This keeps the same intent — the Figma library favors staying close to the original brand hex over maximizing contrast headroom on its one "strong" background token per role.

This is a **deliberate divergence from `color.md`'s JSON-generation value-mapping**, which instead searches for the step that clears WCAG AA contrast (often landing on 700–900 for `content`/`border`/`focus_ring` `-primary`, or for `background`'s `-solid`). The two are allowed to disagree — this Figma collection favors staying close to the original brand hex over maximizing contrast headroom; the JSON output (and the HTML preview) keeps using `color.md`'s contrast-driven steps unchanged. Don't edit `color.md` to match this, and don't edit this file to match `color.md`. If a real contrast problem shows up against a 400–600-range fill inside the Figma file, fix it by picking a different `content.*` token (e.g. use `content.base-primary` instead of `content.white` on that fill) rather than pushing the fill darker — pushing it darker would violate this rule.

Every other role/emphasis/state combination (all `background.*-primary`/`-secondary`/`-tertiary`/`-quaternary`, every other element's `secondary`/`tertiary`/`quaternary` emphasis, and every `base`/`white`/`placeholder`/`disabled` role at any emphasis) keeps exactly the same `Global` alias `color.md`'s value-mapping convention already assigns — this override touches only `content`/`border`/`focus_ring`'s `-primary` roles and `background`'s `-solid` state.

Name the collection exactly `Color` — not `Color / Semantic`, not `Semantic Color`, no suffix.

### Variable scope per element

Set each `Color` variable's Figma **Scopes** property per its element, so the variable picker only offers a variable where it's actually meant to be used:

| Element | Applies to (Figma node/property) |
|---|---|
| `background` | Frame fill, Shape fill (`FRAME_FILL`, `SHAPE_FILL`) |
| `content` | Text fill, Shape fill (`TEXT_CONTENT`, `SHAPE_FILL`) |
| `border` | Stroke (`STROKE_COLOR`) |
| `focus_ring` | Stroke (`STROKE_COLOR`) |
| `overlay` | Frame fill, Shape fill (`FRAME_FILL`, `SHAPE_FILL`) |

Set scopes per-variable (or per-group, if the Figma UI allows bulk-selecting a whole element's variables at once) when each variable is created in Phase 7 — don't leave any `Color` variable on the default "All scopes."

## 3. Collection `Foundation`

Single mode, named `Value`. Holds the Tier 2 semantic scale for every family except color:

* `spacing/[name]` — 12 variables (`none`...`5xl`), each aliasing its `Global` `spacing/space-[N]` counterpart per `spacing-radius-elevation-borderwidth-focusring.md`.
* `radius/[name]` — 13 variables (`none`...`6xl` plus `full`), each aliasing its `Global` `radius/radius-[N]` counterpart.
* `border/[name]` — 4 variables (`sm`/`md`/`lg`/`xl`), each aliasing its `Global` `border/border-[...]` counterpart.
* `typography/[semantic-name]/font-family`, `.../font-size`, `.../line-height`, `.../font-weight`, `.../letter-spacing` — a Figma Variable can only hold one scalar, so each of the 22 `typography.semantic` tokens (`headline-xs-regular`, `body-md-bold`, etc.) becomes a **group of 5 alias variables**, one per property, each aliasing the matching `Global` `typography/*` primitive. This mirrors `typography.md` Tier 2's five-property composite exactly, just decomposed into individually-bindable variables.

Figma **Text Styles** are still the actual thing designers apply to text — build all 22 in Phase 7b's Typography page exactly as before (bind `fontSize` to the `Foundation` variable, or directly to `Global`, either is fine since they hold the same value). The `Foundation` typography variables exist so the semantic-level values are also available as bindable variables, not just baked into Text Styles.

## Migration note for files built under an older structure

If `Color / Primitives`, `Color / Semantic`, `Typography`, or `Spacing & Radius` collections already exist (from a run before this file existed): rebuild fresh `Global`/`Color`/`Foundation` collections per the rules above, rebind every existing node (swatches, text styles, spacing/radius/border demo bindings) to the new variable IDs, then delete the superseded collections. Renaming a variable in place keeps its ID (no rebinding needed); moving a variable's *collection* membership isn't supported by the Plugin API, so anything that needs to end up in a different collection than it started in has to be recreated and rebound.

## Self-check before finishing Phase 7
* Exactly three collections exist: `Global`, `Color`, `Foundation` — no others left over.
* Every `Color` variable aliases a `Global` variable; every `Foundation` variable aliases a `Global` variable; nothing in `Color`/`Foundation` holds a literal value.
* Every `-primary`-emphasis, brand/status-sourced role in `content`/`border`/`focus_ring` resolves to a step in {400, 500, 600} of its palette; every `-solid` state in `background` resolves the same way.
* `background.*-primary`/`-secondary`/`-tertiary`/`-quaternary` are **not** part of the 400–600 clamp — they stay light tints per `color.md`.
* Every non-`-primary`, non-`-solid` role in `Color` still matches `color.md`'s value-mapping convention exactly (unchanged).
* Every `Color` variable's Scopes are set per the element table above — no variable left on the default "All scopes."
* The 22 Text Styles exist and their `fontSize` (at minimum) is variable-bound, not literal.
* Every variable in `Global` is set to **Hidden from publishing**; every variable in `Color` and `Foundation` remains published/visible.
