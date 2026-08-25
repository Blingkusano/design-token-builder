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

* `color/[palette]/[palette]-[step]` — one group per palette **the user actually supplied**: `primary`, `neutral`, `success`, `warning`, `error`, `info` always, plus `secondary` and/or `accent` **only when the user gave one** (see `SKILL.md` Phase 2b's optional-palette rule — a *ไม่มี / None* answer means that group doesn't exist here at all, and no colour is invented to fill it). 11 variables per present palette, plus `color/white`, `color/black`, `color/black-alpha/black-alpha-[N]`, `color/white-alpha/white-alpha-[N]`. Type `COLOR`.
* `typography/font-family/font-family-[slug]` — type `STRING`.
* `typography/font-size/font-size-[N]` — type `FLOAT`.
* `typography/line-height/line-height-[N]` — type `FLOAT`.
* `typography/font-weight/font-weight-[N]` — type `FLOAT`. **Keys stay numeric** (`font-weight-400`, `font-weight-700`) — the `default`/`strong` ladder from `typography.md` is a Tier 2 vocabulary and never appears in `Global`.
* `typography/letter-spacing/letter-spacing-[...]` — type `FLOAT`.
* `spacing/space-[N]` — type `FLOAT`. 17 variables: `0, 2, 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64, 80, 96, 112, 128`.
* `radius/radius-[N]` — type `FLOAT`. **12 variables: `0, 2, 4, 8, 10, 12, 16, 20, 24, 32, 40, 999`.** There is no `radius/radius-full` variable any more — the pill step is `radius/radius-999`. If an older run of this skill created `radius/radius-full`, rename it to `radius/radius-999` (renaming keeps the variable ID, so existing bindings survive) and reset its value to `999`.
* `border/border-[...]` — type `FLOAT` (includes the literal-decimal hairline step, `border/border-0.5`). Unchanged: `0.5`, `1`, `2`, `4`.

This is a direct, literal-value transcription of every `*.global` branch in `color.md`, `typography.md`, and `spacing-radius-elevation-borderwidth-focusring.md` — no aliasing happens inside `Global`, only inside `Color` and `Foundation`, which alias back into it.

### Publishing visibility

Every variable inside `Global` — all of it, every group, no exceptions — must be marked **Hidden from publishing** before this file is published as a library. `Global` exists only so `Color` and `Foundation` have raw values to alias into; it's an internal implementation detail, not something a consuming file should ever bind to directly. Hiding it keeps a published library's variable picker showing only the semantic `Color`/`Foundation` tokens, so downstream files can't accidentally bind to a raw primitive instead of a semantic one.

* **How:** in the Figma UI, select all variables in `Global` (or the whole collection) and toggle the "Hide from publishing" (eye-slash) option from the right-click / publish-review menu. Via the Plugin API, set `hiddenFromPublishing = true` on every `Variable` in the collection.
* **Scope:** this applies to `Global` only. `Color` and `Foundation` stay published/visible as normal — they're the intended public surface.
* **What it doesn't do:** hiding from publishing doesn't delete, rename, or change the value of anything — `Color` and `Foundation` still alias `Global` internally exactly as before within this file. It only controls what a *different* file sees after subscribing to this one as a library.
* **When:** do this once the `Global` collection's variables exist, before (or as part of) the first publish of the file — re-check it any time new `Global` variables are added later, since new variables default to published/visible and won't inherit the hidden state automatically.
* **Not affected by this rule:** the "Global Colors" documentation table on the `  --> Color` Foundation page (per `figma-page-structure.md`) still shows every `Global` primitive's name and hex directly — that's an in-file canvas table, not a published variable binding, so it's unrelated to this rule and needs no change.

## 2. Collection `Color`

Two modes, `Light` and `Dark`. Holds the same `background`/`content`/`border`/`overlay`/`focus_ring` elements and role/emphasis/state keys as `color.md` Tier 2 — **up to 80 variables** (background 32, content 28, border 15, overlay 1, focus_ring 4). Every variable aliases a `Global` color variable; nothing here holds a literal.

**Optional palettes:** the count drops exactly as `color.md`'s conditional-roles table says. No Secondary → drop the five `background/brand_secondary-*` and four `content/brand_secondary-*` variables (**71 total**); no Accent → drop the four `content/accent-*` variables; neither → **67 total**. Don't create a variable whose `Global` alias target doesn't exist, and don't point a would-be `brand_secondary`/`accent` variable at the `primary` ramp to keep the count round.

**`background` follows `color.md`'s current direction, not the old one:** `background/[role]-primary` is the **lightest** step of its family and `secondary`/`tertiary`/`quaternary` step progressively deeper; `background/[role]-solid` is the single dark, contrast-bearing step. Never rebuild `background` with `primary` as the strong step.

**`background/base-plain`** is a flat variable (no emphasis) whose `Light` mode aliases `Global` `color/white` and whose `Dark` mode aliases `Global` `color/black`. It's the only `Color` variable that binds those two primitives.

### Figma-only value override (the 400–600 clamp)

For a semantic key whose role is sourced from a brand or status palette (`brand`, `brand_secondary`, `accent`, `error`, `warning`, `success`, `info` — **not** `base`/`white`/`placeholder`/`plain`/`disabled`) alias it to that palette's own **anchor step** (the exact `Global` step holding the user's original input hex — see `color.md` Tier 1's anchoring rule), **clamped into the 400–600 range**: below 400 → use 400; above 600 → use 600; already 400/500/600 → use as-is.

The clamp now applies to a **narrower set than in earlier versions**, because `color.md` itself pinned several of these values:

| Key | Figma behavior |
|---|---|
| `background/[role]-solid` | **Clamped** (400–600). This is the main remaining divergence — `color.md` searches darker for contrast, Figma stays near the brand hex. |
| `border/[role]-primary` | **Clamped** (400–600). |
| `content/[status]-primary` (`error`/`warning`/`success`/`info`) | **Clamped** (400–600). `color.md` picks these by contrast inside 400–700, so a 700 result gets pulled back to 600 here. |
| `content/brand-*`, `content/brand_secondary-*`, `content/accent-*` | **No clamp — mirror `color.md` exactly** (`primary`=600, `secondary`=**700**, `tertiary`=500, `quaternary`=400). These are already fixed steps; `secondary` at `700` is deliberate and must survive into Figma unchanged. Do not clamp it to 600. |
| `focus_ring/brand-primary`, `focus_ring/brand-secondary` | **No clamp** — `600` and `700` respectively, matching `content/brand-*`. |
| `focus_ring/error-primary` | **Clamped** (400–600). |
| `background/[role]-primary` / `-secondary` / `-tertiary` / `-quaternary` | **Never clamped** — they're light tints (as low as `50`) per `color.md`; clamping would fight that rule directly. |
| Everything else (`base`/`white`/`placeholder`/`plain`/`disabled` at any emphasis, every non-`primary` emphasis on other elements) | Keeps exactly the `Global` alias `color.md`'s value-mapping assigns — unchanged. |

Where the clamp applies it is a **deliberate divergence from `color.md`'s JSON-generation value-mapping**: this Figma collection favors staying close to the original brand hex over maximizing contrast headroom; the JSON output (and the HTML preview) keeps `color.md`'s contrast-driven steps unchanged. The two are allowed to disagree — don't edit `color.md` to match this, and don't edit this file to match `color.md`. If a real contrast problem shows up against a 400–600-range fill inside the Figma file, fix it by picking a different `content/*` token (e.g. `content/base-primary` instead of `content/white` on that fill) rather than pushing the fill darker.

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

* `spacing/[name]` — **12 variables** (`none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, `7xl`), each aliasing its `Global` `spacing/space-[N]` counterpart per `spacing-radius-elevation-borderwidth-focusring.md`. **`spacing/md` aliases `spacing/space-8`** — verify this one explicitly, it moved from `space-16`.
* `radius/[name]` — **12 variables** (`none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, plus `full`), each aliasing its `Global` `radius/radius-[N]` counterpart. **`radius/md` aliases `radius/radius-8`**; `radius/full` aliases `radius/radius-999`.
* `border/[name]` — 4 variables (`sm`/`md`/`lg`/`xl`), each aliasing its `Global` `border/border-[...]` counterpart. Unchanged — `border/md` is still `border-1` (1px).
* `typography/[semantic-name]/font-family`, `.../font-size`, `.../line-height`, `.../font-weight`, `.../letter-spacing` — a Figma Variable can only hold one scalar, so each `typography.semantic` token becomes a **group of 5 alias variables**, one per property, each aliasing the matching `Global` `typography/*` primitive. **The group name uses the semantic weight ladder**, e.g. `typography/headline-xs-default/*`, `typography/body-md-strong/*` — never `-regular`/`-bold`/`-700`. On a default run that's 22 groups (`headline`×6 + `body`×5, × `default`/`strong`) = 110 variables.

Figma **Text Styles** are still the actual thing designers apply to text — build one per `typography.semantic` token in Phase 7b's Typography page (bind `fontSize` to the `Foundation` variable, or directly to `Global`, either is fine since they hold the same value). **Name each Text Style with the semantic ladder name too** (`headline-xs-default`, `body-lg-strong`), matching the variable group. The `Foundation` typography variables exist so the semantic-level values are also available as bindable variables, not just baked into Text Styles.

## Migration note for files built under an older structure

If `Color / Primitives`, `Color / Semantic`, `Typography`, or `Spacing & Radius` collections already exist (from a run before this file existed): rebuild fresh `Global`/`Color`/`Foundation` collections per the rules above, rebind every existing node (swatches, text styles, spacing/radius/border demo bindings) to the new variable IDs, then delete the superseded collections. Renaming a variable in place keeps its ID (no rebinding needed); moving a variable's *collection* membership isn't supported by the Plugin API, so anything that needs to end up in a different collection than it started in has to be recreated and rebound.

Specifically for a file built by 1.0.0 (or an earlier v08/v09 run): `radius/radius-full` → rename to `radius/radius-999` (value `999`); `Foundation` `radius/*` drops from 13 to 12 variables; `spacing/md` and `radius/md` both need repointing to the `8` primitive; every `typography/*-regular` group renames to `*-default` and every `*-bold` group to `*-strong`; add `background/base-plain`; add the four `content/brand_secondary-*` variables.

## Self-check before finishing Phase 7
* Exactly three collections exist: `Global`, `Color`, `Foundation` — no others left over.
* Every `Color` variable aliases a `Global` variable; every `Foundation` variable aliases a `Global` variable; nothing in `Color`/`Foundation` holds a literal value.
* `Color`'s variable count matches the project's palette configuration — 80 with both optional palettes present (background 32 including `base-plain` and the seven `-solid` states, content 28, border 15, overlay 1, focus_ring 4), less the conditional roles otherwise.
* `Global` has a `color/*` group for every supplied palette and **none** for a palette answered *None* — and no `Color` variable references a missing group.
* `background/[role]-primary` is the lightest step of its family in `Light` mode, and is **not** part of the 400–600 clamp.
* `content/brand-secondary`, `content/brand_secondary-secondary`, `content/accent-secondary` all resolve to step `700` — not clamped down to 600.
* `Foundation` `spacing/md` and `radius/md` both resolve to **8**; `radius/full` resolves to `999`; no `radius/radius-full` variable remains anywhere.
* `Foundation` `spacing/*` has 12 variables and `radius/*` has 12 variables.
* Every `Foundation` `typography/*` group and every Text Style is named with the semantic ladder (`-default`/`-strong`/…), and no `Global` `font-weight` variable was renamed away from its numeric key.
* Every `Color` variable's Scopes are set per the element table above — no variable left on the default "All scopes."
* Every variable in `Global` is set to **Hidden from publishing**; every variable in `Color` and `Foundation` remains published/visible.
