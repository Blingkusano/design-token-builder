# Reference: Color (Tier 1 / Tier 2)

Load this file in full before writing any `color` JSON. It is the single source of truth for `color.global` and `color.semantic` — nothing about color lives in `SKILL.md` itself beyond a pointer to this file.

**Scope (3.0.0, MVP1 lean):** two tiers only, one colour mode only. There is **no `color.component` tier**, **no Light/Dark split**, and **no custom/brand theme** — `color.semantic` holds the five elements directly, with no theme key between them.

---
## Tier 1 — `color.global` (primitive layer)

* **Optional palettes — read this first.** Of the eight input colors, **Secondary and Accent are optional**: `SKILL.md` Phase 2b lets the user answer *ไม่มี / None* to either. When they do, that palette is **not generated at any tier** — no `color.global` ramp, no `color.semantic` role, no Figma variable, no preview strip — and **no substitute color is derived from Primary or anywhere else**. Everything below that mentions `secondary`/`accent` is conditional on the user actually having supplied one. The other six (Primary, Neutral, Success, Warning, Error, Info) are always required and always generated.
* **Palette scales:** for each input color the user actually supplied (Primary, Neutral, Success, Warning, Error, Info, plus Secondary and/or Accent when present), generate an 11-step tonal ramp: `50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950`. Key format: `color.global.[palette]-[step]`, e.g. `color.global.primary-600`. `$type: "color"`, `$value` a hex string.
  * Anchor the user's exact input hex at whichever step its lightness naturally lands on (compute via HSL lightness against a target-lightness table per step: `50`≈97%, `100`≈94%, `200`≈86%, `300`≈76%, `400`≈64%, `500`≈50%, `600`≈40%, `700`≈32%, `800`≈24%, `900`≈16%, `950`≈10%) — do not force every input color to sit at `500`. Generate the remaining 10 steps by holding hue/saturation constant and varying lightness to the target-lightness table.
* **Base neutrals:** `color.global.white` = `#FFFFFF`, `color.global.black` = `#000000`.
* **Alpha scale:** `color.global.black-alpha-[N]` and `color.global.white-alpha-[N]` for `N` in `0, 5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100` (percent). `$value` as an `rgba(...)` string, e.g. `rgba(0, 0, 0, 0.50)`.
* Generate exactly this set — no unused palette steps, no missing ones (`white`, `black`, and both alpha scales are the fixed base set and are always generated). Every `color.semantic.*` token in this project must resolve back to one of these. A palette the user answered *None* to contributes zero primitives; a palette they supplied contributes all 11.

## Tier 2 — `color.semantic` (element/role/emphasis layer)

**This project uses the `[element].[role]-[emphasis]` pattern.** This **replaces** any older `content`/`bg`/`border`-with-subgroups taxonomy — do not use that shape.

**Pattern:** `element` is the JSON nesting branch (`color.semantic.[element]`), and `role-emphasis` is the flat, single-hyphen kebab-case key inside it — e.g. `color.semantic.background.base-primary`. There is no theme segment (`light`/`dark`/custom) anywhere in the path.

**Disambiguation rule (narrow exception to "no underscores"):** when a role's own name is a multi-word compound whose last word could itself be mistaken for a trailing emphasis value (`primary`/`secondary`/`tertiary`/`quaternary`), join that role's internal words with an **underscore** instead of a hyphen — e.g. a role literally named "brand secondary" becomes `brand_secondary`, not `brand-secondary` (which would be indistinguishable from `brand` role + `secondary` emphasis). Only apply this when the collision is real; don't underscore compound role names that don't collide.

**Elements (closed set):** `background`, `content`, `border`, `overlay` — documented fully below. **`focus_ring`** is also a `color.semantic` element by this same pattern, but its full role list and value-mapping are documented in `spacing-radius-elevation-borderwidth-focusring.md` (grouped there per explicit user request) — still nest it under `color.semantic.focus_ring` in the actual JSON, exactly like the other four. **`shadow`** also appears in the source taxonomy using this pattern, but shadows stay under the separate top-level `elevation` branch (see the other reference file) rather than folding into `color.semantic` — `elevation`'s JSON location is fixed by the overall skeleton (below) and isn't part of this override.

**Emphasis (closed set, up to four per role):** `primary`, `secondary`, `tertiary`, `quaternary`. Not every role uses all four; status roles and `disabled` only go up to `secondary`; `white`/`placeholder`/`plain` have no emphasis at all (flat, single token).

Directionality differs by element, and this is the part that changed most recently — read it carefully:

* **`content` and `border`:** `primary` = strongest / most-used / most-accessible variant; `secondary`/`tertiary`/`quaternary` = progressively softer. (Exception: the three fixed-step roles in `content` — see the `content` value-mapping bullet.)
* **`background`: emphasis runs from lightest to least-light.** `primary` is the **lightest tint in the ramp** — the plainest, most-used app surface — and `secondary` → `tertiary` → `quaternary` step **progressively deeper / more tinted** (still all light tints, never a contrast-bearing fill). This is inverted from the old rule, where `primary` was the most-present tint; do not carry the old direction forward.
* The strong, contrast-bearing fill a `background` role needs (a primary button's fill, a filled badge) is carried **only** by the separate `-solid` state — never by any emphasis level.

**No interactive states.** Don't generate `-hover`/`-active`/`-pressed`/`-focus` variants — those belonged to the component tier, which this version doesn't have. **`solid` is the one suffix that exists, and it applies only to `background`:** every `background` role that carries emphasis levels also gets exactly one `-solid` token (e.g. `background.brand-solid`) — always generate it, it isn't a speculative add-on. See the `background` value-mapping bullet below for what it resolves to.

**Per-element role list (closed set — the full roster).** Counts below are the **maximum**, i.e. what a project with every optional palette supplied produces; see the conditional-role table underneath.
* **`background`** (32 tokens): `base` (4: primary/secondary/tertiary/quaternary — the app's base background, **+1 `base-solid`**, **+1 `base-plain`**), `brand` (4, **+1 `brand-solid`**), `brand_secondary` (4 — underscore per the disambiguation rule, **+1 `brand_secondary-solid`**), `error`/`warning`/`success`/`info` (2 each, **+1 `-solid` each**), `placeholder` (flat), `white` (flat), `disabled` (2, no `-solid` — a disabled surface never gets the solid treatment).
  * **`base-plain` (flat — no emphasis):** the one deliberately absolute surface in the whole system. `$value` is a **direct alias to a Tier 1 base neutral, not to the neutral ramp**: `{color.global.white}` (`#FFFFFF`). It exists for surfaces that must be pure white regardless of how the neutral ramp was generated from the user's neutral input hex (sheets, print-like canvases, media backdrops). It is still an alias — never write the literal hex at Tier 2.
* **`content`** (28 tokens): `base` (4), `brand` (4), `brand_secondary` (4), `accent` (4), `error`/`warning`/`success`/`info` (2 each), `placeholder` (flat), `white` (flat), `disabled` (2).
* **`border`** (15 tokens): `base` (2), `brand` (2), `error`/`warning`/`success`/`info` (2 each), `white` (flat), `disabled` (2).
* **`overlay`** (1 token): no role segment — just `primary` (`color.semantic.overlay.primary`).

**Conditional roles (driven by `SKILL.md` Phase 2b's optional-palette answers):**

| User answered | Roles removed | New element counts |
|---|---|---|
| Secondary = *None* | `background.brand_secondary-*` (4 emphasis + `-solid` = 5) and `content.brand_secondary-*` (4) | background **27**, content **24** |
| Accent = *None* | `content.accent-*` (4) | content **24** |
| Both = *None* | all of the above (13) | background **27**, content **20** |

`border`, `focus_ring`, and `overlay` carry no secondary- or accent-sourced role, so they are **never** affected — 15 / 4 / 1 in every configuration. `brand` (sourced from Primary) is never affected either. Removing a role means the key simply doesn't exist: no `null`, no empty object, no placeholder alias, and no colour improvised to stand in for it.

**Known asymmetries (intentional, not gaps):** `background` has `brand_secondary` and `base-plain`; `content` has `accent` and `brand_secondary`; `border` has neither — each element only carries the roles it needs.

**Known coverage gap:** the background-agnostic, alpha-based text-color pattern (`content.opacity-primary/secondary/tertiary/quaternary` in an older taxonomy) has no equivalent role here. If a design needs that, fall back to the nearest `content.base-*` token rather than inventing a new role.

**Value-mapping convention** (how `$value` aliases are chosen): each role maps to one `color.global` palette family — `base`→neutral, `brand`→primary, `brand_secondary`→secondary, `accent`→accent, `error`/`warning`/`success`/`info`→their matching status palette, `overlay.primary`→`black-alpha-50`. `emphasis` then picks the step within that family. **The tables below are the authority — copy them; do not re-derive a step from the prose around them.** Only the two searches that are explicitly written as numbered steps (`background`’s `-solid`, and `border`’s `primary`) are computed at generation time. These values are the single source for every output: the HTML preview renders them and the Figma `Color` collection mirrors them alias-for-alias (`figma-variable-structure.md`), so the colours a user approves in the preview are the colours that reach Figma.

* **`background` — emphasis levels (fixed steps, no judgment call):** these are surfaces, not fills — no contrast requirement applies to any of them, and none may land on a dark step. Use exactly these steps; never derive them:

  | Emphasis | Step |
  |---|---|
  | `primary` | `50` |
  | `secondary` | `100` |
  | `tertiary` | `200` |
  | `quaternary` | `300` |

  Status roles (`error`/`warning`/`success`/`info`) carry only `primary` and `secondary` — the first two rows. `placeholder` and `disabled-primary` take `100`; `disabled-secondary` takes `200`; `white` aliases `{color.global.white}`. The accessibility-critical usage lives on `-solid`, never here.
* **`background` — the `-solid` state (one per role):** run this exact search and stop at the first hit — don't reason about it per role. The search is **bounded to `400`–`700`**, so a solid fill is never too light to carry text and never darker than the brand hex can stand (the `-solid` fill was the one token family with no ceiling — it used to fall through to `950`):
  1. **candidates** = the family's steps from **`max(anchor, 400)`** (the anchor is where the user's input hex landed, per Tier 1) **downward, stopping at `700`**: e.g. anchor `500` → `[500, 600, 700]`; anchor `300` → `[400, 500, 600, 700]`; anchor `800` → `[700]`.
  2. take the **first** candidate whose contrast against `{color.global.white}` is **≥ 4.5:1**.
  3. if no candidate reaches 4.5:1, use `700`, and record in the hand-off notes which text token (`content.white` or `content.base-primary`) clears WCAG AA on it — or that neither does. `SKILL.md` Phase 2's contrast-check round normally catches this before generation.

  `-solid` is always exactly one alias per role, and is the darkest token that role owns. This search governs `base`, `brand`, `error`, `warning`, `success`, and `info`.
  * **Fixed-step exception — `brand_secondary-solid` = step `500`.** This one role does **not** run the search. `background.brand_secondary-solid` always aliases `{color.global.secondary-500}`, in every run, so a secondary-brand fill reads as the brand's own colour rather than a darkened version of it — matching the step `brand-solid` lands on whenever the primary hex is saturated enough to pass on its own. Generate `500` verbatim; never step it darker to chase contrast.
  * **Where the contrast burden moves for that role:** because `brand_secondary-solid` is pinned, a light or mid-tone secondary (a yellow, orange, or lime) will *not* clear 4.5:1 against white text. The fill is then correct and the **text** is what must change: compute the contrast of `secondary-500` against both `content.white` and `content.base-primary`, and **record in the hand-off notes which one clears WCAG AA** (per `SKILL.md` Phase 3) so designers pair the fill with that text token. Never resolve the failure by darkening the fill. If neither text token clears it, say so in the hand-off notes and recommend `background.brand-solid` for text-bearing fills instead.
* **`content` — fixed default steps inside the `400`–`700` band, then verify.** Every `content` token resolves to a step in `{400, 500, 600, 700}` of its family; never `300` or lighter, never `800` or darker. Start from this table rather than re-deriving it per role:

  | Emphasis | Step |
  |---|---|
  | `primary` | `700` |
  | `secondary` | `600` |
  | `tertiary` | `500` |
  | `quaternary` | `400` |

  Applies as written to `base`. **Status roles** carry only `primary`/`secondary`: `warning`, `success` and `info` use the table's first two rows as written (`700`/`600`); `error` sits one step lighter (`600`/`500`), because red already reaches AA at a lighter step. `placeholder` = `500`. `disabled-primary`/`disabled-secondary` = `500`/`400`. `white` aliases `{color.global.white}`.

  Then **verify numerically** against `background.base-primary` (WCAG AA per `SKILL.md` Phase 3 — 4.5:1 normal text, 3:1 large). The band is a hard floor and ceiling: where a token only reaches AA-large, **record it in the hand-off notes rather than moving it out of the band**.
  * **Fixed-step exception — `brand`, `brand_secondary`, `accent`:** these three roles **never** run the contrast search. They always resolve to these exact steps of their family, in every run:

    | Emphasis | Global step |
    |---|---|
    | `primary` | `600` |
    | `secondary` | `700` |
    | `tertiary` | `500` |
    | `quaternary` | `400` |

    Note that `secondary` (`700`) is deliberately *darker* than `primary` (`600`) for these three roles — that is intended, not a typo, and it is the one place in the system where emphasis order and lightness order diverge. Generate these steps verbatim; do not "correct" them toward a contrast-derived value.
  * `placeholder` and `white` are flat (no emphasis); their steps are already given in the `content` table above.
* **`border` — run this exact search:** candidates in order — `[200, 300, 400, 500, 600, 700]`. `primary` is the **first** candidate whose contrast against `background.base-primary` is **≥ 3:1** (Phase 3's UI-component minimum). `secondary` is the decorative variant: the step **two positions earlier in that same ordering** than `primary` (i.e. two steps lighter; floor at `200`). `white` aliases `{color.global.white}`; `disabled-primary`/`disabled-secondary` = `300`/`200`.

**Scoping note on `elevation`:** shadow tokens don't fold into `color.semantic.shadow` — `elevation` stays its own top-level branch, sibling to `color`, per the locked overall skeleton below. Only its semantic *keys* changed (`0`/`1`/`2`/`3` → `none`/`sm`/`md`/`lg`) to match this file's `emphasis`-style vocabulary; see the combined spacing/radius/elevation/border/focus_ring reference for the generation rules.

## No Tier 3

There is **no `color.component` branch** in this version. Don't generate one, don't create per-component keys anywhere else in the JSON, and don't add interactive `-hover`/`-active`/`-pressed` states to Tier 2 to stand in for it. Designers bind `color.semantic.*` tokens directly.

## Locked JSON skeleton (color-relevant excerpt)
```jsonc
{
  "color": {
    "global":   { /* Tier 1, per above */ },
    "semantic": { "background": {}, "content": {}, "border": {}, "focus_ring": {}, "overlay": {} }
    /* no theme key (light/dark/custom), no "component" branch */
  }
}
```

## Self-check before output (run silently)
* Every key in `color.global` and `color.semantic` is kebab-case (the only exception: underscore inside a role name that would otherwise collide with an emphasis word, per the disambiguation rule).
* `color.global` contains a ramp for **exactly** the palettes the user supplied — if Secondary or Accent was answered *None*, `grep` the output for `secondary-` / `accent-` and confirm zero hits at every tier.
* `color` has exactly two branches, `global` and `semantic` — no `component` branch — and `color.semantic` has no theme key (`light`/`dark`/custom) between it and its elements.
* `color.semantic` has exactly the five elements `background`/`content`/`border`/`focus_ring`/`overlay`, with counts matching the project's palette configuration: background **32** / content **28** with both optional palettes present, dropping per the conditional-roles table above when either is absent. `border` 15, `overlay` 1, `focus_ring` — see the other reference file — in every configuration.
* `background.*-primary` is the **lightest** step of its family and each following emphasis is deeper — if `primary` is darker than `secondary` anywhere in `background`, the direction is wrong.
* Every `background` role that carries emphasis levels (`base`, `brand`, `brand_secondary`, `error`, `warning`, `success`, `info`) has exactly one `-solid` token, and no `background` emphasis token holds a dark/contrast-driven value.
* Every searched `background.*-solid` (`base`, `brand`, `error`, `warning`, `success`, `info`) resolves to a step in **`400`–`700`** — none lighter than `400`, none darker than `700`.
* `background.brand_secondary-solid` resolves to step **`500`** exactly — verify literally; it is the one `-solid` token that never runs the contrast search. Then confirm the hand-off notes name which text token (`content.white` or `content.base-primary`) clears WCAG AA against `secondary-500`.
* `background.base-plain` exists, is flat (no emphasis), and aliases `{color.global.white}`.
* Every `content.*` token resolves to a step in `{400, 500, 600, 700}` — zero `content` tokens outside that band (`content.white` is the one flat exception, aliasing `{color.global.white}`).
* `content.brand-*`, and (where their palettes exist) `content.brand_secondary-*` / `content.accent-*`, resolve to exactly `600` / `700` / `500` / `400` for `primary` / `secondary` / `tertiary` / `quaternary` — verify each present role literally, no contrast substitutions.
* Every `color.semantic.*` `$value` aliases `color.global.*` — zero literal hex/rgba values at Tier 2.
* All WCAG AA contrast pairs (per `SKILL.md` Phase 3) actually pass — re-verify numerically, don't eyeball it — except a failure the user explicitly kept in `SKILL.md` Phase 2's contrast-check round, which must then appear in the hand-off notes. Where a fixed-step `content` role (`brand`/`brand_secondary`/`accent`) fails against a given surface, fix it by moving the *surface* (use a lighter `background` emphasis, or `base-plain`) — never by moving the fixed step.
