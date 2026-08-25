# Reference: Color (Tier 1 / Tier 2 / Tier 3)

Load this file in full before writing any `color` JSON. It is the single source of truth for `color.global`, `color.semantic`, and `color.component` — nothing about color lives in `SKILL.md` itself beyond a pointer to this file.

---
## Tier 1 — `color.global` (primitive layer)

* **Optional palettes — read this first.** Of the eight input colors, **Secondary and Accent are optional**: `SKILL.md` Phase 2b lets the user answer *ไม่มี / None* to either. When they do, that palette is **not generated at any tier** — no `color.global` ramp, no `color.semantic` role, no Figma variable, no preview strip — and **no substitute color is derived from Primary or anywhere else**. Everything below that mentions `secondary`/`accent` is conditional on the user actually having supplied one. The other six (Primary, Neutral, Success, Warning, Error, Info) are always required and always generated.
* **Palette scales:** for each input color the user actually supplied (Primary, Neutral, Success, Warning, Error, Info, plus Secondary and/or Accent when present), generate an 11-step tonal ramp: `50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950`. Key format: `color.global.[palette]-[step]`, e.g. `color.global.primary-600`. `$type: "color"`, `$value` a hex string.
  * Anchor the user's exact input hex at whichever step its lightness naturally lands on (compute via HSL lightness against a target-lightness table per step: `50`≈97%, `100`≈94%, `200`≈86%, `300`≈76%, `400`≈64%, `500`≈50%, `600`≈40%, `700`≈32%, `800`≈24%, `900`≈16%, `950`≈10%) — do not force every input color to sit at `500`. Generate the remaining 10 steps by holding hue/saturation constant and varying lightness to the target-lightness table.
* **Base neutrals:** `color.global.white` = `#FFFFFF`, `color.global.black` = `#000000`.
* **Alpha scale:** `color.global.black-alpha-[N]` and `color.global.white-alpha-[N]` for `N` in `0, 5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100` (percent). `$value` as an `rgba(...)` string, e.g. `rgba(0, 0, 0, 0.50)`.
* Generate exactly this set — no unused primitives, no missing ones. Every `color.semantic.*` token in this project must resolve back to one of these. A palette the user answered *None* to contributes zero primitives; a palette they supplied contributes all 11.

## Tier 2 — `color.semantic.[theme]` (element/role/emphasis layer)

**This project uses the `[element].[role]-[emphasis](-[state])` pattern.** This **replaces** any older `content`/`bg`/`border`-with-subgroups taxonomy — do not use that shape.

**Pattern:** `element` is the JSON nesting branch (`color.semantic.[theme].[element]`), and `role-emphasis(-state)` is the flat, single-hyphen kebab-case key inside it — e.g. `color.semantic.light.background.base-primary`.

**Disambiguation rule (narrow exception to "no underscores"):** when a role's own name is a multi-word compound whose last word could itself be mistaken for a trailing emphasis value (`primary`/`secondary`/`tertiary`/`quaternary`), join that role's internal words with an **underscore** instead of a hyphen — e.g. a role literally named "brand secondary" becomes `brand_secondary`, not `brand-secondary` (which would be indistinguishable from `brand` role + `secondary` emphasis). Only apply this when the collision is real; don't underscore compound role names that don't collide.

**Elements (closed set):** `background`, `content`, `border`, `overlay` — documented fully below. **`focus_ring`** is also a `color.semantic` element by this same pattern, but its full role list and value-mapping are documented in `spacing-radius-elevation-borderwidth-focusring.md` (grouped there per explicit user request) — still nest it under `color.semantic.[theme].focus_ring` in the actual JSON, exactly like the other four. **`shadow`** also appears in the source taxonomy using this pattern, but shadows stay under the separate top-level `elevation` branch (see the other reference file) rather than folding into `color.semantic` — `elevation`'s JSON location is fixed by the overall skeleton (below) and isn't part of this override.

**Emphasis (closed set, up to four per role):** `primary`, `secondary`, `tertiary`, `quaternary`. Not every role uses all four; status roles and `disabled` only go up to `secondary`; `white`/`placeholder`/`plain` have no emphasis at all (flat, single token).

Directionality differs by element, and this is the part that changed most recently — read it carefully:

* **`content` and `border`:** `primary` = strongest / most-used / most-accessible variant; `secondary`/`tertiary`/`quaternary` = progressively softer. (Exception: the three fixed-step roles in `content` — see the `content` value-mapping bullet.)
* **`background`: emphasis runs from lightest to least-light.** `primary` is the **lightest tint in the ramp** — the plainest, most-used app surface — and `secondary` → `tertiary` → `quaternary` step **progressively deeper / more tinted** (still all light tints, never a contrast-bearing fill). This is inverted from the old rule, where `primary` was the most-present tint; do not carry the old direction forward. In a Dark theme the same rule mirrors: `primary` is the darkest/plainest surface step and each following emphasis steps progressively lighter.
* The strong, contrast-bearing fill a `background` role needs (a primary button's fill, a filled badge) is carried **only** by the separate `-solid` state — never by any emphasis level.

**State (optional, open-ended):** append a trailing `-[state]` only when a Tier 3 component token needs an interactive variant beyond the base `role-emphasis` set (`hover`, `active`, `pressed`, `focus`, etc.) — not a closed set, don't pre-generate speculative state variants at the base semantic list. **`solid` is the one exception, and it applies only to `background`:** every `background` role that carries emphasis levels also gets exactly one `-solid` token (e.g. `background.brand-solid`) — always generate it, it isn't a speculative add-on. See the `background` value-mapping bullet below for what it resolves to.

**Per-element role list (closed set — the full roster).** Counts below are the **maximum**, i.e. what a project with every optional palette supplied produces; see the conditional-role table underneath.
* **`background`** (32 tokens): `base` (4: primary/secondary/tertiary/quaternary — the app's base background, **+1 `base-solid`**, **+1 `base-plain`**), `brand` (4, **+1 `brand-solid`**), `brand_secondary` (4 — underscore per the disambiguation rule, **+1 `brand_secondary-solid`**), `error`/`warning`/`success`/`info` (2 each, **+1 `-solid` each**), `placeholder` (flat), `white` (flat), `disabled` (2, no `-solid` — a disabled surface never gets the solid treatment).
  * **`base-plain` (flat — no emphasis):** the one deliberately absolute surface in the whole system. `$value` is a **direct alias to a Tier 1 base neutral, not to the neutral ramp**: Light theme → `{color.global.white}` (`#FFFFFF`), Dark theme → `{color.global.black}` (`#000000`). It exists for surfaces that must be pure white / pure black regardless of how the neutral ramp was generated from the user's neutral input hex (sheets, print-like canvases, media backdrops). It is still an alias — never write the literal hex at Tier 2.
* **`content`** (28 tokens): `base` (4), `brand` (4), `brand_secondary` (4), `accent` (4), `error`/`warning`/`success`/`info` (2 each), `placeholder` (flat), `white` (flat), `disabled` (2).
* **`border`** (15 tokens): `base` (2), `brand` (2), `error`/`warning`/`success`/`info` (2 each), `white` (flat), `disabled` (2).
* **`overlay`** (1 token): no role segment — just `primary` (`color.semantic.light.overlay.primary`).

**Conditional roles (driven by `SKILL.md` Phase 2b's optional-palette answers):**

| User answered | Roles removed | New element counts |
|---|---|---|
| Secondary = *None* | `background.brand_secondary-*` (4 emphasis + `-solid` = 5) and `content.brand_secondary-*` (4) | background **27**, content **24** |
| Accent = *None* | `content.accent-*` (4) | content **24** |
| Both = *None* | all of the above (13) | background **27**, content **20** |

`border`, `focus_ring`, and `overlay` carry no secondary- or accent-sourced role, so they are **never** affected — 15 / 4 / 1 in every configuration. `brand` (sourced from Primary) is never affected either. Removing a role means the key simply doesn't exist: no `null`, no empty object, no placeholder alias, and no colour improvised to stand in for it.

**Known asymmetries (intentional, not gaps):** `background` has `brand_secondary` and `base-plain`; `content` has `accent` and `brand_secondary`; `border` has neither — each element only carries the roles it needs.

**Known coverage gap:** the background-agnostic, alpha-based text-color pattern (`content.opacity-primary/secondary/tertiary/quaternary` in an older taxonomy) has no equivalent role here. If a component needs that, fall back to the nearest `content.base-*` token rather than inventing a new role.

**Value-mapping convention** (how `$value` aliases are chosen): each role maps to one `color.global` palette family — `base`→neutral, `brand`→primary, `brand_secondary`→secondary, `accent`→accent, `error`/`warning`/`success`/`info`→their matching status palette, `overlay.primary`→`black-alpha-50`. `emphasis` then picks the step within that family:

* **`background` — emphasis levels (light tints, lightest-first):** `primary` takes the **lightest usable step of the family** (typically `50`, or `100` where `50` is indistinguishable from the page), and `secondary`/`tertiary`/`quaternary` walk **one step deeper each** (e.g. `50 → 100 → 200 → 300`). No numeric floor and no contrast requirement applies to any of the four — they are surfaces, not fills, and the accessibility-critical usage lives on `-solid`. Never let a `background` emphasis token land on a dark/contrast-driven step.
* **`background` — the `-solid` state (one per role):** start from the palette's own anchor step (where the user's input hex landed, per Tier 1) and move toward **darker** steps only as far as needed; pick the first step that clears WCAG AA against whatever text sits on it (per `SKILL.md` Phase 3). Never search past that point, and never pick a step lighter than the anchor. If the anchor already passes (e.g. `brand-500` when the input hex is dark/saturated enough), `-solid` uses it directly; otherwise keep stepping darker (typically `700`–`900`). `-solid` is always exactly one alias per role, and is the darkest token that role owns. This contrast-driven search governs `base`, `brand`, `error`, `warning`, `success`, and `info`.
  * **Fixed-step exception — `brand_secondary-solid` = step `500`.** This one role does **not** run the search. `background.brand_secondary-solid` always aliases `{color.global.secondary-500}`, in every theme and every run, so a secondary-brand fill reads as the brand's own colour rather than a darkened version of it — matching the step `brand-solid` lands on whenever the primary hex is saturated enough to pass on its own. Generate `500` verbatim; never step it darker to chase contrast.
  * **Where the contrast burden moves for that role:** because `brand_secondary-solid` is pinned, a light or mid-tone secondary (a yellow, orange, or lime) will *not* clear 4.5:1 against white text. The fill is then correct and the **text** is what must change: at Tier 3, compute the contrast of `secondary-500` against both `content.white` and `content.base-primary`, and alias whichever clears WCAG AA (per `SKILL.md` Phase 3). Never resolve the failure by darkening the fill. If neither text token clears it, don't ship the pairing — use `background.brand-solid` for that component instead and note the substitution.
* **`content` — generated inside the `400`–`700` band.** Every `content` token resolves to a step in `{400, 500, 600, 700}` of its family; never `300` or lighter, never `800` or darker. Within that band, pick by contrast against the `background` token the text is meant to sit on (WCAG AA per `SKILL.md` Phase 3 — 4.5:1 normal text, 3:1 large text) — `primary` is the most-accessible/darkest, stepping lighter through `secondary`/`tertiary`/`quaternary`. This contrast-driven selection applies to `base`, `error`, `warning`, `success`, `info`, and `disabled`.
  * **Fixed-step exception — `brand`, `brand_secondary`, `accent`:** these three roles **never** run the contrast search. They always resolve to these exact steps of their family, in every theme and every run:

    | Emphasis | Global step |
    |---|---|
    | `primary` | `600` |
    | `secondary` | `700` |
    | `tertiary` | `500` |
    | `quaternary` | `400` |

    Note that `secondary` (`700`) is deliberately *darker* than `primary` (`600`) for these three roles — that is intended, not a typo, and it is the one place in the system where emphasis order and lightness order diverge. Generate these steps verbatim; do not "correct" them toward a contrast-derived value.
  * `placeholder` and `white` stay flat: `placeholder` takes a neutral step inside the same `400`–`700` band, `white` aliases `{color.global.white}`.
* **`border`:** `primary` is the accessible/default border step (validate 3:1 against its background per Phase 3's UI-component contrast minimum); `secondary` is a lighter, decorative variant.

**Scoping note on `elevation`:** shadow tokens don't fold into `color.semantic.[theme].shadow` — `elevation` stays its own top-level branch, sibling to `color`, per the locked overall skeleton below. Only its semantic *keys* changed (`0`/`1`/`2`/`3` → `none`/`sm`/`md`/`lg`) to match this file's `emphasis`-style vocabulary; see the combined spacing/radius/elevation/border/focus_ring reference for the generation rules.

## Tier 3 — `color.component`

Path: `color.component.[component].[element].[property]-[state]`, e.g. `color.component.button.container.bg-hover`. `$value` MUST alias a Tier 2 `color.semantic.[theme].*` token only (never Tier 1 directly, never a literal), through the matching element: fills alias `background.*` (use a `-solid` token where the component needs the strong/main fill, e.g. a primary button's container; use a `-primary`/`-secondary`/etc. light-tint token where it needs a subtle surface tint, and `base-plain` where the surface must be absolute white/black), text/icon colors alias `content.*`, stroke/outline colors alias `border.*`, backdrop/scrim colors alias `overlay.*`, focus outlines alias `focus_ring.*`. Also nested here (still under `color.component`, per the locked overall JSON skeleton): a component's padding/gap aliases `spacing.semantic.*`, its corner rounding aliases `radius.semantic.*`, its label/value text aliases `typography.semantic.*`, and its shadow aliases `elevation.semantic.*` — never a Tier 1 primitive or a literal, for any of these.

Where Tier 2 doesn't have an exact role/emphasis/state for what a component needs, fall back to the nearest available Tier 2 token per the known-gaps notes above rather than reaching into Tier 1 or inventing a new Tier 2 token. **This is also how an absent optional palette is handled:** a component that would have aliased `background.brand_secondary-*` or `content.accent-*` in a project without a Secondary/Accent palette aliases the matching `brand` token instead — it never reaches for a colour that wasn't generated, and it never triggers generating one.

## Locked JSON skeleton (color-relevant excerpt)
```jsonc
{
  "color": {
    "global":    { /* Tier 1, per above */ },
    "semantic":  {
      "light": { "background": {}, "content": {}, "border": {}, "focus_ring": {}, "overlay": {} }
      /* any custom theme name gets its own sibling object with the same 5 elements */
    },
    "component": { /* Tier 3, per above */ }
  }
}
```

## Self-check before output (run silently)
* Every key in `color.global`, `color.semantic`, `color.component` is kebab-case (the only exception: underscore inside a role name that would otherwise collide with an emphasis word, per the disambiguation rule).
* `color.global` contains a ramp for **exactly** the palettes the user supplied — if Secondary or Accent was answered *None*, `grep` the output for `secondary-` / `accent-` and confirm zero hits at every tier.
* `color.semantic.[theme]` has exactly the five elements `background`/`content`/`border`/`focus_ring`/`overlay`, with counts matching the project's palette configuration: background **32** / content **28** with both optional palettes present, dropping per the conditional-roles table above when either is absent. `border` 15, `overlay` 1, `focus_ring` — see the other reference file — in every configuration.
* `background.*-primary` is the **lightest** step of its family and each following emphasis is deeper — if `primary` is darker than `secondary` anywhere in `background`, the direction is wrong.
* Every `background` role that carries emphasis levels (`base`, `brand`, `brand_secondary`, `error`, `warning`, `success`, `info`) has exactly one `-solid` token, and no `background` emphasis token holds a dark/contrast-driven value.
* `background.brand_secondary-solid` resolves to step **`500`** exactly — verify literally, in both themes; it is the one `-solid` token that never runs the contrast search. Then confirm every Tier 3 fill using it pairs with a text token that clears WCAG AA against `secondary-500`.
* `background.base-plain` exists, is flat (no emphasis), and aliases `{color.global.white}` in Light / `{color.global.black}` in Dark — nothing else in `background` aliases those two primitives.
* Every `content.*` token resolves to a step in `{400, 500, 600, 700}` — zero `content` tokens outside that band (`content.white` is the one flat exception, aliasing `{color.global.white}`).
* `content.brand-*`, and (where their palettes exist) `content.brand_secondary-*` / `content.accent-*`, resolve to exactly `600` / `700` / `500` / `400` for `primary` / `secondary` / `tertiary` / `quaternary` — verify each present role literally, no contrast substitutions.
* Every `color.semantic.*` `$value` aliases `color.global.*` — zero literal hex/rgba values at Tier 2.
* Every `color.component.*` `$value` aliases Tier 2 (`color.semantic.*`, `spacing.semantic.*`, `radius.semantic.*`, `typography.semantic.*`, or `elevation.semantic.*`) — zero Tier 1 aliases, zero literals, at Tier 3.
* All WCAG AA contrast pairs (per `SKILL.md` Phase 3) actually pass — re-verify numerically, don't eyeball it. Where a fixed-step `content` role (`brand`/`brand_secondary`/`accent`) fails against a given surface, fix it by moving the *surface* (use a lighter `background` emphasis, or `base-plain`) — never by moving the fixed step.
