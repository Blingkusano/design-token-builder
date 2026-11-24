# Reference: Verification Checklist

Run every Part 1 check before delivering the HTML preview and token JSON. Run the Part 2 checks before returning the PART 2 report (only when the user opted into Figma). Each check is pass/fail — fix a failure before delivering, or, where the check allows it, list it in the hand-off notes.

**Part 1 — token JSON + HTML preview (check on every run):**
* [ ] **Intake Held:** onboarding used one intake form, a suggestion round and a contrast-check round only when needed; every suggested or adjusted colour was shown as a swatch with its hex code (or as a `name — #HEX` list where no inline form tool exists).
* [ ] **Contrast Round Honoured:** every colour that failed Round 3's checks was shown to the user with its variants, and every failure the user kept is listed in the hand-off notes.
* [ ] **Solid Range Held:** every searched `background.*-solid` resolves to a step in `400`–`700`.
* [ ] **Even Type Sizes:** every `typography.global.font-size-*` is an even integer of at least `10px`, matching `reference/typography.md`'s scale table for the chosen base (14 / 16 / 18).
* [ ] **Both First Deliverables Shipped:** the run produced an HTML preview file **and** a token JSON file, together, as the first output — neither replaced by an inline code block or deferred.
* [ ] **Background Direction Inverted:** every `background.*-primary` is the lightest step of its family, deepening through `-quaternary`, with `-solid` the single dark step.
* [ ] **`background.base-plain` Present:** flat token aliasing `{color.global.white}`.
* [ ] **Content Band Held:** every `content.*` token sits in `400`–`700`, and `brand`/`brand_secondary`/`accent` are pinned to `600`/`700`/`500`/`400`.
* [ ] **`brand_secondary-solid` Pinned:** resolves to step `500`, and the hand-off notes name the text token that clears WCAG AA against it.
* [ ] **Optional Palettes Honoured:** for every palette the user answered *ไม่มี / None* to, zero `color.global.*` steps and zero `color.semantic.*` roles were generated — and no substitute colour was invented anywhere.
* [ ] **Weight Ladder Applied:** every `typography.semantic` name ends in a ladder level (`default`/`strong` on a fresh run) — no `-regular`/`-bold`/numeric suffixes.
* [ ] **`md` = 8px:** both `spacing.semantic.md` and `radius.semantic.md` resolve to `8px`.
* [ ] **Two-Tier Separation Complete:** across `color`, `elevation`, `typography`, `spacing`, `radius` — and nothing beyond Tier 2.
* [ ] **MVP1 Scope Held:** no `color.component` branch, no theme key (`light`/`dark`/custom) under `color.semantic`, no `rem` value, and no platform question was asked.
* [ ] **Typography Standardized:** every `typography.semantic.*` value is 100% aliases into `typography.global`.
* [ ] **Spacing & Radius Standardized:** every `spacing.semantic.*`/`radius.semantic.*` value aliases `*.global` — zero literals.
* [ ] **Naming Clean:** every key `kebab-case`, zero stray underscores outside the documented disambiguation exception.

**Part 2 — Figma (check only when the user opted into Phase 7/8; skip entirely otherwise):**
* [ ] **Scope Locking Active:** 100% of variables use targeted layout scopes.
* [ ] **Clean Auditing:** 0 hardcoded colors/sizes across the production canvas.
* [ ] **Matches the Preview:** every `Color` variable aliases exactly the `Global` step the token JSON names — zero differences from the HTML preview.
* [ ] **Figma Collection Shape:** exactly `Global`/`Color`/`Foundation`, each with one mode named `Value`, per `reference/figma-variable-structure.md` — no leftover legacy collections.
* [ ] **Global Publishing Visibility:** every `Global` variable is hidden from publishing; `Color` and `Foundation` remain published, per `reference/figma-variable-structure.md`.
* [ ] **Figma Page Shape:** exactly the skeleton in `reference/figma-page-structure.md` — Cover, `FOUNDATION` + six `  --> ` sub-pages, Utilities, Changelog — no "Getting Started", no `COMPONENT` page, no stray pages.

### Example — PART 2 report (abbreviated, from a real run)

```
Pages: Cover / --- / FOUNDATION (--> Color, Typography, Spacing, Radius, Shadow, Focus ring) / --- / Utilities / Changelog
Collections (one mode "Value" each): Global 160 — all hidden from publishing · Color 76 · Foundation 138
Matches the preview: 76/76 Color aliases equal the token JSON's steps · 103/103 Global colours equal the JSON
Phase 8: 8a naming — 0 duplicates · 8b binding — 0 hardcoded values on 7 pages
Deviations: none
```
