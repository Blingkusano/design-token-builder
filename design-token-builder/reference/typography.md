# Reference: Typography (Tier 1 / Tier 2)

Load this file in full before writing any `typography` JSON.

---
## The locked size multiplier table

Pulled up whenever Phase 2c's Visual Scaling Check is answered "Yes." **Fixed — never ask the user to choose a ratio/multiplier, never offer `1.125`/`1.200`/`1.250`-style alternatives.**

| Size (px, base = 16) | Multiplier (× base) |
|---|---|
| 10 | ×0.625 |
| 12 | ×0.75 |
| 14 | ×0.875 |
| 16 (base) | ×1 |
| 18 | ×1.125 |
| 20 | ×1.25 |
| 24 | ×1.5 |
| 28 | ×1.75 |
| 32 | ×2 |
| 40 | ×2.5 |
| 48 | ×3 |

If the user's base font size isn't `16`, scale every step proportionally (`multiplier × user's base`) rather than using the literal px values above.

## Tier 1 — `typography.global` (decomposed by property, never pre-bundled)

* `font-size-[N]` — `$type: "dimension"`, e.g. `font-size-16` = `"16px"`. Generate only the sizes the Tier 2 scale below actually needs, pulled from the table above (or its proportional equivalents).
* `font-weight-[N]` — `$type: "fontWeight"`, numeric value (e.g. `font-weight-700` = `700`).
* `line-height-[N]` — `$type: "number"`. `N` = `round(value × 100)` (the 4.1 decimal-encoding rule in the shared naming convention), e.g. line-height `1.5` → key `line-height-150`, `$value` stays `1.5`.
* `letter-spacing-[neg-][N]` — `$type: "dimension"`. `N` = `round(abs(em value) × 1000)`; insert the word `neg` as its own segment only when negative. E.g. `-0.02em` → key `letter-spacing-neg-20`, `$value` stays `"-0.02em"`; `0em` → key `letter-spacing-0`.
* `font-family-[slug]` — `$type: "fontFamily"`, e.g. `font-family-noto-sans-thai` = `"Noto Sans Thai"`. One per distinct font family in play.
* Generate exactly what Tier 2 needs — no unused primitives, no missing ones.

## Tier 2 — `typography.semantic` (composite alias layer)

**Current scale (per explicit user request — supersedes any earlier `display`/`headline`/`title`/`body`/`label` 15-level scale):**

* **`display` is removed entirely.**
* **`headline`** — anchored T-shirt scale, 6 steps: `xs` (18px, smallest) → `sm` (20px) → `md` (24px) → `lg` (28px) → `xl` (32px) → `2xl` (40px, largest).
* **`body`** — anchored T-shirt scale, 5 steps: `xs` (10px, smallest) → `sm` (12px) → `md` (14px) → `lg` (16px, = the base font size) → `xl` (18px, largest).
* **`title` and `label` are removed** (per explicit user decision) — any component that previously used a `title-*` or `label-*` style should reference the nearest `headline-*` or `body-*` step instead (see the component-mapping notes in `SKILL.md` Phase 6 / the color reference's Tier 3 rule).
* **Weight variants:** every one of the 11 size steps above comes in exactly two weights — `regular` (400) and `bold` (700) — no `semibold`/`medium`. Final token name: `[family]-[tshirt]-[weight]`, e.g. `headline-xs-regular`, `headline-xs-bold`, `body-lg-regular`, `body-lg-bold`. That's `(6 + 5) × 2 = 22` total `typography.semantic` tokens.
* Each token: `$type: "typography"`, `$value` an object whose every property (`fontFamily`/`fontSize`/`fontWeight`/`lineHeight`/`letterSpacing`) is an alias string into `typography.global` — never a bare number or px/em string:
  ```jsonc
  "headline-xs-bold": {
    "$type": "typography",
    "$value": {
      "fontFamily":    "{typography.global.font-family-noto-sans-thai}",
      "fontSize":      "{typography.global.font-size-18}",
      "fontWeight":    "{typography.global.font-weight-700}",
      "lineHeight":    "{typography.global.line-height-130}",
      "letterSpacing": "{typography.global.letter-spacing-0}"
    }
  }
  ```
* Suggested line-height/letter-spacing per step (adjust only if the user's font family needs different values): `headline` tightens as it gets larger (`xs`≈1.3 down to `2xl`≈1.15, with `xl`/`2xl` picking up slight negative tracking `neg-10`/`neg-20`); `body` stays loose for readability (`xs`≈1.4 up to `xl`≈1.5, with `xs`/`sm` picking up a touch of positive tracking `10`/`20` for legibility at small sizes).

## Generation order (Tier 1 before Tier 2 — do not skip)
1. From the base font size and the multiplier table, derive the raw sizes the 11-step scale needs and write each as its own `typography.global.font-size-[N]` primitive.
2. Do the same for every distinct weight (`400`, `700` only), line-height, and letter-spacing the scale needs, plus one `font-family-[slug]` primitive per font family in play.
3. Only then build the 22 `typography.semantic.*` composite tokens, each `$value` assembled entirely from aliases into the Tier 1 primitives just created.

## Self-check before output (run silently)
* Every decimal/negative Tier 1 primitive follows the encoding rule (`line-height-[N]`, `letter-spacing-neg-[N]`) while its `$value` still holds the real, unscaled number/string.
* `typography.semantic` has exactly 22 names (`headline`×6 sizes×2 weights + `body`×5 sizes×2 weights) — no `display`, `title`, or `label` entries, no `semibold`/`medium` weight variants.
* Every `typography.semantic.*` value's five properties are all alias strings into `typography.global` — zero literal numbers or px/em strings at Tier 2.
* `typography.global.font-weight-*` has exactly two entries: `400` and `700`.
