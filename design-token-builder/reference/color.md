# Reference: Color (Tier 1 / Tier 2 / Tier 3)

Load this file in full before writing any `color` JSON. It is the single source of truth for `color.global`, `color.semantic`, and `color.component` — nothing about color lives in `SKILL.md` itself beyond a pointer to this file.

---
## Tier 1 — `color.global` (primitive layer)

* **Palette scales:** for each of the eight input colors (Primary, Secondary, Neutral, Accent, Success, Warning, Error, Info), generate an 11-step tonal ramp: `50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950`. Key format: `color.global.[palette]-[step]`, e.g. `color.global.primary-600`. `$type: "color"`, `$value` a hex string.
  * Anchor the user's exact input hex at whichever step its lightness naturally lands on (compute via HSL lightness against a target-lightness table per step: `50`≈97%, `100`≈94%, `200`≈86%, `300`≈76%, `400`≈64%, `500`≈50%, `600`≈40%, `700`≈32%, `800`≈24%, `900`≈16%, `950`≈10%) — do not force every input color to sit at `500`. Generate the remaining 10 steps by holding hue/saturation constant and varying lightness to the target-lightness table.
* **Base neutrals:** `color.global.white` = `#FFFFFF`, `color.global.black` = `#000000`.
* **Alpha scale:** `color.global.black-alpha-[N]` and `color.global.white-alpha-[N]` for `N` in `0, 5, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100` (percent). `$value` as an `rgba(...)` string, e.g. `rgba(0, 0, 0, 0.50)`.
* Generate exactly this set — no unused primitives, no missing ones. Every `color.semantic.*` token in this project must resolve back to one of these.

## Tier 2 — `color.semantic.[theme]` (element/role/emphasis layer)

**This project uses the `[element].[role]-[emphasis](-[state])` pattern.** This **replaces** any older `content`/`bg`/`border`-with-subgroups taxonomy — do not use that shape.

**Pattern:** `element` is the JSON nesting branch (`color.semantic.[theme].[element]`), and `role-emphasis(-state)` is the flat, single-hyphen kebab-case key inside it — e.g. `color.semantic.light.background.base-primary`.

**Disambiguation rule (narrow exception to "no underscores"):** when a role's own name is a multi-word compound whose last word could itself be mistaken for a trailing emphasis value (`primary`/`secondary`/`tertiary`/`quaternary`), join that role's internal words with an **underscore** instead of a hyphen — e.g. a role literally named "brand secondary" becomes `brand_secondary`, not `brand-secondary` (which would be indistinguishable from `brand` role + `secondary` emphasis). Only apply this when the collision is real; don't underscore compound role names that don't collide.

**Elements (closed set):** `background`, `content`, `border`, `overlay` — documented fully below. **`focus_ring`** is also a `color.semantic` element by this same pattern, but its full role list and value-mapping are documented in `spacing-radius-elevation-borderwidth-focusring.md` (grouped there per explicit user request) — still nest it under `color.semantic.[theme].focus_ring` in the actual JSON, exactly like the other four. **`shadow`** also appears in the source taxonomy using this pattern, but shadows stay under the separate top-level `elevation` branch (see the other reference file) rather than folding into `color.semantic` — `elevation`'s JSON location is fixed by the overall skeleton (below) and isn't part of this override.

**Emphasis (closed set, up to four per role):** `primary`, `secondary`, `tertiary`, `quaternary`. Consistent meaning everywhere: **primary = strongest / most-used variant**, `secondary`/`tertiary`/`quaternary` = progressively softer/lighter. For `content` and `border`, `primary` is the darkest/most-accessible step in the family — same directionality as always. **`background` is the one exception:** all four of its emphasis levels are light tints (see the `background` value-mapping bullet below) — the strong/solid fill usage a `background` role needs (e.g. a primary button's fill) is carried by the separate `-solid` state instead, not by `primary` emphasis. Not every role uses all four; status roles and `disabled` only go up to `secondary`; `white`/`placeholder` have no emphasis at all (flat, single token).

**State (optional, open-ended):** append a trailing `-[state]` only when a Tier 3 component token needs an interactive variant beyond the base `role-emphasis` set (`hover`, `active`, `pressed`, `focus`, etc.) — not a closed set, don't pre-generate speculative state variants at the base semantic list. **`solid` is the one exception, and it applies only to `background`:** every `background` role that carries emphasis levels also gets exactly one `-solid` token (e.g. `background.brand-solid`) — always generate it, it isn't a speculative add-on. See the `background` value-mapping bullet below for what it resolves to.

**Per-element role list (closed set — the full roster):**
* **`background`** (31 tokens): `base` (4: primary/secondary/tertiary/quaternary — the app's base background, **+1 `base-solid`**), `brand` (4, **+1 `brand-solid`**), `brand_secondary` (4 — underscore per the disambiguation rule; this is the source taxonomy’s `brand_secondary` role, **+1 `brand_secondary-solid`**), `error`/`warning`/`success`/`info` (2 each, **+1 `-solid` each**), `placeholder` (flat), `white` (flat), `disabled` (2, no `-solid` — a disabled surface never gets the solid treatment). That's the original 24 plus 7 new `-solid` tokens, one per role that carries emphasis levels.
* **`content`** (24 tokens): `base` (4), `brand` (4), `accent` (4), `error`/`warning`/`success`/`info` (2 each), `placeholder` (flat), `white` (flat), `disabled` (2).
* **`border`** (15 tokens): `base` (2), `brand` (2), `error`/`warning`/`success`/`info` (2 each), `white` (flat), `disabled` (2).
* **`overlay`** (1 token): no role segment — just `primary` (`color.semantic.light.overlay.primary`).

**Known asymmetries (intentional, not gaps):** `background` has `brand_secondary`; `content` has `accent`; neither mirrors the other 1:1 — each element only carries the roles it needs.

**Known coverage gap:** the background-agnostic, alpha-based text-color pattern (`content.opacity-primary/secondary/tertiary/quaternary` in an older taxonomy) has no equivalent role here. If a component needs that, fall back to the nearest `content.base-*` token rather than inventing a new role.

**Value-mapping convention** (how `$value` aliases are chosen): each role maps to one `color.global` palette family — `base`→neutral, `brand`→primary, `brand_secondary`→secondary, `accent`→accent, `error`/`warning`/`success`/`info`→their matching status palette, `overlay.primary`→`black-alpha-50`. `emphasis` then picks the step within that family:
* For `background`, all four emphasis levels are light tints of the family, `primary` being the closest/most-present tint and `secondary`/`tertiary`/`quaternary` progressively lighter or more decorative — **no numeric floor**: unlike `content`/`border`, a `background` role's `primary` does not need to sit in any particular 400–600-style band; pick whichever light step (as low as `100`) reads cleanly for that role, since the accessibility-critical solid usage now lives on `-solid` instead. **`-solid`** is the one strong/dark token per role and carries what `primary` used to mean here: starting from the palette's own anchor step (where the user's input hex landed, per `color.md` Tier 1) and moving toward darker steps only as far as needed, pick the first step that clears WCAG AA against whatever text sits on it (per `SKILL.md` Phase 3) — never search past that point, and never pick a step lighter than the anchor. If the anchor step already passes on its own (e.g. `brand-500`, when the input hex is already dark/saturated enough), `-solid` uses that step directly — that's the `background-brand-solid` example. If it doesn't, keep stepping darker (typically landing around `700`–`900`) until one does. `-solid` is always exactly one alias per role. Example: `background.brand-solid` aliases whichever `color.global.primary-*` step passes contrast this way, `background.brand-primary` aliases a light tint like `color.global.primary-100`.
* For `content`, `primary` is always the darkest, most-accessible step in the family (validated 4.5:1 against the background it's meant for); `secondary`/`tertiary`/`quaternary` step progressively lighter/less saturated for decorative or lower-emphasis text.
* For `border`, `primary` is the accessible/default border step (validate 3:1 against its background per Phase 3's UI-component contrast minimum); `secondary` is a lighter, decorative variant.

**Scoping note on `elevation`:** shadow tokens don't fold into `color.semantic.[theme].shadow` — `elevation` stays its own top-level branch, sibling to `color`, per the locked overall skeleton below. Only its semantic *keys* changed (`0`/`1`/`2`/`3` → `none`/`sm`/`md`/`lg`) to match this file's `emphasis`-style vocabulary; see the combined spacing/radius/elevation/border/focus_ring reference for the generation rules.

## Tier 3 — `color.component`

Path: `color.component.[component].[element].[property]-[state]`, e.g. `color.component.button.container.bg-hover`. `$value` MUST alias a Tier 2 `color.semantic.[theme].*` token only (never Tier 1 directly, never a literal), through the matching element: fills alias `background.*` (use a `-solid` token where the component needs the strong/main fill, e.g. a primary button's container; use a `-primary`/`-secondary`/etc. light-tint token where it needs a subtle surface tint), text/icon colors alias `content.*`, stroke/outline colors alias `border.*`, backdrop/scrim colors alias `overlay.*`, focus outlines alias `focus_ring.*`. Also nested here (still under `color.component`, per the locked overall JSON skeleton): a component's padding/gap aliases `spacing.semantic.*`, its corner rounding aliases `radius.semantic.*`, its label/value text aliases `typography.semantic.*`, and its shadow aliases `elevation.semantic.*` — never a Tier 1 primitive or a literal, for any of these.

Where Tier 2 doesn't have an exact role/emphasis/state for what a component needs, fall back to the nearest available Tier 2 token per the known-gaps notes above rather than reaching into Tier 1 or inventing a new Tier 2 token.

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
* `color.semantic.[theme]` has exactly the five elements `background`/`content`/`border`/`focus_ring`/`overlay`, each with the exact role/emphasis counts listed above (background 31, content 24, border 15, overlay 1, focus_ring — see the other reference file).
* Every `background` role that carries emphasis levels (`base`, `brand`, `brand_secondary`, `error`, `warning`, `success`, `info`) has exactly one `-solid` token, and no `background.*-primary` (or `-secondary`/`-tertiary`/`-quaternary`) token is a dark/contrast-driven value — those are all light tints now, `-solid` carries the dark one.
* Every `color.semantic.*` `$value` aliases `color.global.*` — zero literal hex/rgba values at Tier 2.
* Every `color.component.*` `$value` aliases Tier 2 (`color.semantic.*`, `spacing.semantic.*`, `radius.semantic.*`, `typography.semantic.*`, or `elevation.semantic.*`) — zero Tier 1 aliases, zero literals, at Tier 3.
* All WCAG AA contrast pairs (per `SKILL.md` Phase 3) actually pass — re-verify numerically, don't eyeball it.
