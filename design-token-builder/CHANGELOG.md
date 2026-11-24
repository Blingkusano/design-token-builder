# Changelog

All notable changes to this skill are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.1.1] - 2026-11-24

Restructure, plus two output-format rules and a minimum type size. Token values are identical to 3.1.0, except the body sizes at base `14`. This release fixes the skill-md-auditor findings: `SKILL.md` is down from 33,384 to 23,718 characters (24,098 bytes), under the 25,000 limit.

### Added
- **`reference/intake-rounds.md`:** Phase 2 Rounds 1–3, moved verbatim from `SKILL.md`, plus an example Round 3 card.
- **`reference/verification-checklist.md`:** the former Appendix C, moved verbatim, plus an example PART 2 report.
- **`SKILL.md` Example Output:** compact examples of a colour token, the Round 1 intake, a Round 3 card and the PART 2 report.
- **Hand-off notes format (Phase 1 step 3):** one fixed table with the columns Token · Value · Against · Ratio · Status · Use with. When there is nothing to report, it is a single line instead.
- **Final sign-off (Phase 8):** always lists five items — the Figma link, the page list, collection counts, the 8a/8b results and the hand-off notes.

### Changed
- **`reference/figma-variable-structure.md`:** now holds the Figma scope mapping and pitfalls (former Appendices A–B).
- **`reference/typography.md`:**
  - The multiplier table is "always applied". The old wording referred to the removed visual-scaling question.
  - The even-integer rounding rule and the 14/16/18 scale table now live here.
  - **Minimum type size `10px`.** At base `14` the body row was `8, 10, 12, 14, 16`, which put `body-xs` at `8px`. It is now `10, 12, 14, 16, 18`, the same as base `16`. Bases `16` and `18` are unchanged. The Even Type Sizes check in `verification-checklist.md` now includes the floor.
- **`reference/color.md`:** the Status-role `content` note said three of the four roles sit one step darker than the table. The steps were right; the sentence was wrong. `warning`/`success`/`info` match the table, and `error` sits one step lighter.
- **`SKILL.md` Phase 4:** "Tier 1/2/3 rules" now reads "Tier 1/2 rules".
- **Secondary and Accent questions:** they use the `แนะนำให้` label from the intake form instead of the old "Suggest options for me".
- **PART 2 hand-off brief:** now also points the subagent at the Part 2 section of `verification-checklist.md`.

### Removed
- **Duplicated rules:**
  - the conciseness rule in Purpose (kept in Do);
  - the inline-JSON rule in Phase 1 step 3 (kept in Don't);
  - "Do not proceed" in Phase 2 (kept in Don't).
- **Long rationale:** the PART 2 hand-off explanation is shortened to one sentence.

## [3.1.0] - 2026-11-24

Onboarding is now one form plus rounds that run only when needed, colours are shown as swatches, and the Figma file matches the HTML preview exactly. The token JSON shape is unchanged. A few colour values can differ from 3.0.0 for the same answers: `background.*-solid` is now bounded, and Figma no longer clamps.

### Added
- **Phase 2 Round 1 — intake form.** All seven questions are asked at once, with defaults pre-filled. Where no inline form tool exists, the user gets a copy-and-fill template instead.
- **Phase 2 Round 2 — suggestion round.** It runs only when a field was answered *แนะนำให้*. Options are shown as swatch cards with hex codes, and every group ends with a `ใส่ hex เอง` option. Neutral and Status now accept *แนะนำให้*, and Status gets 3 harmonised sets of four. Every suggestion must already pass Round 3.
- **Phase 2 Round 3 — contrast check.** It runs only when a colour fails. The check covers:
  - `content.[role]-primary` text at 4.5:1;
  - each `background.[role]-solid`, where `content.white` or `content.base-primary` must reach 4.5:1.

  Each failing colour gets three choices: the closest variant that passes 4.5:1, one that passes 3:1, or keep the colour (the failure goes in the hand-off notes). A variant may not produce a token value that another role already uses in the same element. The system never picks for the user.
- **Typography rounding.** The base font size is one of 14, 16 or 18. Every derived size is rounded to the nearest even integer, with ties rounding up. The resulting scales are written out in `SKILL.md`.
- **Hand-off brief:** now includes the Primary anchor step, which the Cover page binds to.
- **Appendix C checks:**
  - Part 1: Intake Held, Contrast Round Honoured, Solid Range Held and Even Type Sizes.
  - Part 2: Matches the Preview.

### Changed
- **`reference/color.md`:** the `background.*-solid` search is bounded to `400`–`700`. It used to fall through to `950`. If no step passes, it uses `700` and records which text token clears AA.
- **`reference/figma-variable-structure.md`:** every `Color` variable now aliases exactly the `Global` step named in the token JSON, so Figma matches the HTML preview.
- **Colour tokens named to the user:** they always include their element (`background.*`, `content.*`, `border.*`).
- **Phase 3:** contrast failures the user keeps in Round 3 are generated as-is and listed in the hand-off notes.

### Removed
- **The Figma-only 400–600 clamp.** It caused three problems:
  - preview/Figma mismatches on up to 16 keys;
  - duplicated or inverted `content.[status]-primary`/`-secondary` pairs;
  - a conflict over `background.brand_secondary-solid`.

  The `background.*-solid` range above replaces the "not too light, not too dark" job the clamp did.
- **The visual-scaling question.** Both answers produced the same sizes.

## [3.0.0] - 2026-11-23

MVP1 lean release, forked from **1.2.0** (not from 2.0.0 — the 2.0.0 Studio/engine line is separate and untouched). **Breaking:** the token JSON shape changes, so a file generated by 1.2.0 is not identical to one generated by 3.0.0 for the same answers. The skill now produces one brand, one colour mode, for a mobile app, stopping at Tier 2.

### Removed

- **Component tokens (Tier 3).** `SKILL.md` Phase 1 step 3 ("Map Components") and the whole Phase 6 component list (Button, Input, Selection, Tag, Card, Modal, Tabs, Toast, Progress) are gone; Phase 6 is kept as an empty "removed" stub so Phase 7/8 numbering is stable. `reference/color.md` loses its `color.component` section, the `component` key in the locked skeleton, and its Tier 3 self-check line. Interactive `-hover`/`-active`/`-pressed` states, which only existed for Tier 3, are no longer generated.
- **Dark mode.** Every Light/Dark table in `reference/color.md` collapses to a single step column (the old Light values). The `background` dark-mirror rule, `border`'s dark candidate list, and `base-plain`'s `{color.global.black}` alias are removed. The HTML preview's Semantic tables lose the Dark column and its dark-ground styling (Name / Value / Description). The Figma `Color` collection becomes single-mode (`Value`), and the `  --> Color` page tables lose the Dark Value column.
- **Custom / brand themes and multi-brand.** Phase 2c "Theme Configuration" (Dark/Light Y/N, custom theme loop) is removed. The frontmatter description, Purpose and Use Cases no longer mention multi-brand or multi-theme.
- **Theme key in token paths.** `color.semantic.light.background.*` becomes `color.semantic.background.*` (same for `content`, `border`, `overlay`, `focus_ring`).
- **Platform choice.** Phase 2e "Target Platform" is removed. The target is fixed to Mobile App (iOS / Android); dimensions are written in DTCG `px` and read 1:1 as `pt` / `dp`. No `rem` values, no Webview touch-target rule, and the rem→px Figma translation note is replaced by "write a bare number".
- **Audit as a user-facing use.** The frontmatter description no longer lists "audit" among the things a user can ask for, and the Use Case about auditing an existing token set now covers updating/extending only. Phase 8's naming and binding checks stay — they are the skill's own QA of the Figma file it built, not a feature offered to the user.
- **Appendix B Pitfall 2 (Fragmented Theme Scaling)**, Phase 8c **Cross-Theme Switching**, and the Appendix C **Theme Synchronized** check. Pitfalls 3–4 are renumbered 2–3.

### Changed

- Interview is now seven questions: project name, five palette questions, typography. Typography moves from 2d to 2c.
- `brand_secondary-solid`'s contrast note: with no Tier 3 to pair it in, the run records in the hand-off notes which text token (`content.white` or `content.base-primary`) clears WCAG AA against `secondary-500`.
- `border.secondary` is now stated as "two steps lighter than `primary`, floor `200`". 1.2.0 said "two positions further along the ordering", which contradicted its own "(lighter in Light)" note; 3.0.0 keeps the lighter intent.
- Appendix C gains an **MVP1 Scope Held** check; the Figma collection-shape check now requires one `Value` mode per collection.

### Fixed

- `SKILL.md` Appendix A listed scopes that don't exist in Figma (`COMPONENT`, `FILL`, `WIDTH`/`HEIGHT`) and `TEXT_CONTENT` for text colour, contradicting `reference/figma-variable-structure.md`. Now `GAP`, `CORNER_RADIUS`, `FRAME_FILL`/`SHAPE_FILL`, `TEXT_FILL`, `WIDTH_HEIGHT`.
- `reference/html-preview-structure.md` pointed at "Phase 2d" for the default font; now 2c.

### Migration from a 1.2.0 Figma file

The `Color` collection's existing `Dark` mode is left in place (removing a mode is destructive) and reported as a deviation; the first mode is renamed `Value` and receives the values. See `reference/figma-variable-structure.md`.

## [1.2.0] - 2026-10-07

Efficiency release. **No token name, count, or value changes** — a file generated by 1.1.1 and one generated by 1.2.0 are identical for the same answers. What changes is how the run gets there: the Figma half now runs in its own agent context, the colour value rules became lookup tables instead of prose to interpret, and three Figma Plugin API constraints that cost a failed call each are now written down. Measured against a full 1.1.1 run (185 tool calls, 41.7M cached input tokens), these cut roughly 28% of the run's token cost and 5–7 minutes of wall clock.

### Changed

- **PART 2 (Figma) is handed to a subagent instead of running inline.** `SKILL.md` Phase 1 step 6 now launches one general-purpose subagent with a four-item brief — the token JSON file **path**, the Figma URL, the project name, which optional palettes exist, and the scope. The subagent reads the JSON itself and reads the two Figma reference files itself. PART 2 is the longer half of the run (~2× PART 1's tool calls) and was carrying all of PART 1's generator script, contrast exploration and HTML source on every single call; a fresh context drops ~107k tokens from each of them. Falls back to running inline when the session has no subagent tool.
- **The two Figma follow-up questions are now asked together, up front** (Phase 1 step 5), as one two-part choice that sets the scope to `variables` or `full`. PART 2 runs in one pass, so its scope has to be known before it starts. The Phase 7 checkpoint is reported by the subagent and presented by the parent.
- **`reference/color.md`'s Tier 2 value-mapping is now tables, not prose.** `background` emphasis, `content` default steps, `placeholder`, `disabled` and `white` are given as explicit per-theme step tables to copy; `background`'s `-solid` and `border`'s `primary` are the only two values still computed at run time, and both are now written as numbered searches with an explicit candidate order. This removes the per-role, per-theme judgment that dominated generation-time reasoning.

### Fixed

- **`reference/figma-variable-structure.md` listed an invalid Figma scope.** `content/*` was documented as `TEXT_CONTENT`, which only applies to `STRING` variables bound to text characters; a `COLOR` variable rejects it. Corrected to `TEXT_FILL`.
- **The hairline variable's Figma name is now documented as `border/border-0-5`.** Figma rejects `.` anywhere in a variable name — `createVariable('border/border-0.5', …)` throws and rolls back every variable created earlier in the same call. The token key keeps its documented literal decimal (`border.global.border-0.5`); only the Figma variable name diverges.

### Added

- **A "Plugin API constraints" section in `reference/figma-page-structure.md`** covering the three traps that silently produce a wrong-looking page: `resize()` setting *both* sizing modes to `FIXED` (which collapses every table cell to 1px), `figma.createAutoLayout()` returning a frame with an opaque white fill (which reads as a hardcoded value in the Phase 8b binding audit), and `strokeAlign = 'OUTSIDE'` being clipped by an auto-layout parent. Each with the fix, plus a note to verify them from returned geometry rather than a screenshot.
- **A matching section in `reference/figma-variable-structure.md`** on variable naming, scopes, creating variables in bulk rather than in small batches, and not re-pasting values that already exist as variables.
- Self-check lines for all of the above in both Figma reference files.

### Notes on tightened defaults

Three places where 1.1.1 left a judgment call now have a fixed answer. All three match what 1.1.1 produced in practice, so output is unchanged — but a run that previously chose differently will now be consistent:

- `background.*-primary` is step `50` (Light) / `950` (Dark), not "the lightest usable step, typically 50 or 100".
- `content.placeholder` is step `500`; `content.disabled-primary`/`-secondary` are `500`/`400` Light and `600`/`700` Dark.
- `background.*-solid` resolves to the same step in both themes.

## [1.1.1] - 2026-08-25

Patch release. One value rule changes; the rest is audit remediation on `SKILL.md`. Token names and counts are unchanged from 1.1.0, but `background.brand_secondary-solid` resolves to a different step, so regenerate if your project has a Secondary palette.

### Changed

- **`background.brand_secondary-solid` is now pinned to step `500`** instead of running the contrast-driven search. A secondary-brand fill now reads as the brand's own colour rather than a darkened version of it. The accessibility burden moves to the text on top: Tier 3 must compare `secondary-500` against both `content.white` and `content.base-primary` and alias whichever clears WCAG AA, never darken the fill. If neither clears it, the component falls back to `background.brand-solid` and the substitution is stated. Every other `-solid` role (`base`, `brand`, `error`, `warning`, `success`, `info`) keeps the contrast-driven search unchanged.
- `SKILL.md` Phase 7 no longer restates the scope of the Figma 400–600 clamp. That summary had drifted out of sync with `reference/figma-variable-structure.md`, which narrowed the clamp in 1.1.0; the file now points at that table as the single owner of the rule.
- Appendix C is split into **Part 1** (checked every run) and **Part 2** (checked only when the user opted into the Figma phases), so a token-only run no longer shows five permanently unmet Figma boxes.

### Added

- A consolidated **Do / Don't** section in `SKILL.md`, indexing the MUST/NEVER rules that were previously scattered across six phases, each pointing back to the phase that owns it.
- A `brand_secondary-solid` row in Appendix C and a matching line in `reference/color.md`'s self-check.

## [1.1.0] - 2026-08-24

Structural changes to the color, typography, spacing and radius scales, plus a locked deliverable order. **Not drop-in compatible with 1.0.0 output:** semantic token names and counts change, so a token file generated by 1.0.0 cannot be swapped for a 1.1.0 file without remapping. Regenerate rather than hand-merge.

### Added

- `background.base-plain` — a flat, emphasis-less token aliasing `{color.global.white}` in Light and `{color.global.black}` in Dark, for surfaces that must be absolute white/black regardless of how the neutral ramp was generated. `background` goes from 31 to 32 tokens.
- `content.brand_secondary-*` (4 tokens). `content` goes from 24 to 28 tokens.
- An explicit **"ไม่มี / None"** answer for the Secondary and Accent onboarding questions (Phase 2b), with an Optional-palette rule block spelling out what is skipped at every tier. Answering None generates no Tier 1 ramp and no Tier 2 role for that palette — in the JSON, the Figma collections, and the HTML preview alike — and never substitutes a derived colour. Primary, Neutral and the four status colours stay required.
- A `Typeface of the preview shell` section in `reference/html-preview-structure.md`: the preview's own UI font is now the token set's font family (`Noto Sans Thai` by default) loaded from Google Fonts with a system fallback, rather than a system stack.
- A dark ground for the `Mode = Dark` column of the semantic colour tables, specified as one continuous rounded panel from the header cell through the last row. Dark-theme swatches are near-black by definition and were unreadable on the page's white surface.
- A `100vh` bottom-padding guard on `main#app` and a topmost-intersecting-section rule for the sidebar scroll-spy, alongside 1.0.0's click and edge-override rules.
- Six verification rows in Appendix C covering the changes above.

### Changed

- **`background` emphasis direction is inverted.** `-primary` is now the *lightest* tint of its family and `-secondary` → `-tertiary` → `-quaternary` step progressively deeper; the single dark, contrast-bearing step stays on `-solid`. Previously `-primary` was the most-present tint.
- **`content` is generated inside a `400`–`700` band.** `brand`, `brand_secondary` and `accent` are pinned to fixed steps in every theme and every run — `primary`=600, `secondary`=700, `tertiary`=500, `quaternary`=400 — and never run the contrast search. `secondary` being darker than `primary` for those three roles is deliberate. Every other `content` role picks by contrast inside the same band.
- `focus_ring.brand-primary` / `-secondary` follow `content.brand-*` at 600 / 700.
- **Typography semantic weight names** use the relative ladder `lightest · lighter · light · default · strong · stronger · strongest`, anchored at global `regular` (400) = `default`. A fresh run always emits exactly `default` and `strong`, so `headline-xs-regular` becomes `headline-xs-default` and `-bold` becomes `-strong`. Tier 1 keys stay numeric (`font-weight-400`).
- **`radius.global` relocked** to `0, 2, 4, 8, 10, 12, 16, 20, 24, 32, 40, 999`. `radius-999` replaces the old `radius-full` (9999px) primitive; `full` survives only as the Tier 2 semantic name. `radius.semantic` goes from 13 to 12 tokens. `spacing.global` is unchanged.
- **`spacing.semantic.md` and `radius.semantic.md` both anchor at `8px`** (spacing's `md` was 16px), with every other label shifting accordingly. `border.semantic.md` stays 1px.
- Phase 1 step 4 now states that every run's first output is exactly two files, in order — the HTML preview then the token JSON — with an explicit ban on substituting an inline code block for the JSON file or deferring the preview.
- The Figma 400–600 clamp narrowed again: it no longer touches `content.brand-*`, `content.brand_secondary-*`, `content.accent-*` or `focus_ring.brand-*`, which now mirror the JSON's fixed steps exactly. `content.brand-secondary` must reach Figma as step 700, unclamped.
- `Version` field changed from `1.0.0` to `1.1.0`; the skill description now names both deliverables.

## [1.0.0] - 2026-08-21

First release under semantic versioning. Replaces the filename-based scheme (`design-token-builder-v01` … `-v09`) — the version number now lives here and in git tags, not in the skill name. No change to the token schema, naming convention, or output format: a run of 1.0.0 produces the same result as a run of v09.

### Added

- Onboarding question 9, **Target Platform**, in Phase 2e. The platform choice was already assumed by Phase 1 step 2 ("based on platform selections") but was never actually asked — the section only described the conventions for each platform.
- `CHANGELOG.md` (this file).
- `INSTRUCTION.html` — a standalone how-to page covering setup (skill download, Claude install, Figma file, Figma connector, brand colour/font prep) and the generation run.
- A greedy longest-first path-lookup rule for `resolve()` in `reference/html-preview-structure.md`. A plain `split('.')` breaks on `border.global.border-0.5`, whose key contains a literal decimal, leaving the raw alias string rendered on the page.
- Sidebar `.active` rules in `reference/html-preview-structure.md`: click now sets the active link directly with the scroll-spy observer suppressed during the animation, plus a top/bottom edge override. Previously the spec drove `.active` purely from an `IntersectionObserver`, so the last section (Focus ring) could never activate — the document runs out of scroll before its heading reaches the trigger line.

### Changed

- Skill renamed from `design-token-builder-v09` to `design-token-builder`.
- `Version` field changed from `v09` to `1.0.0`.
- `Author / Editor` changed from the placeholder `Design Token Builder Team` to a named owner.
- `reference/figma-page-structure.md` now points at `CHANGELOG.md` instead of `SKILL.md`'s removed changelog section.

### Removed

- The `## Changelog` section from `SKILL.md`; its contents are preserved below.
- A duplicated `---` horizontal rule before `PART 2`.
- A FigJam file key and node id from `reference/color.md`, which identified an internal reference board. The identifiers were documentation-only — no phase ever read them, and the naming pattern they cited is fully specified in the file itself. Three now-dangling mentions of "the reference board" in the same file were reworded to "the source taxonomy".

---

## Earlier history (pre-SemVer)

Migrated verbatim from the `## Changelog` section of `SKILL.md` at v09.

### v09

Version bump, published as a separate skill alongside v08 rather than overwriting it; ships all v08 fixes unchanged.

### v08

Explicit-trigger description, About/Use Cases/worked example added; removed Phase 8 Figma component-building. Renamed `border-width`→`border` (literal `border-0.5` hairline step), `focusring`→`focus_ring`; `spacing`/`radius`'s smallest step renamed to `none`; added `background.*-solid` state (`background`'s `-primary` is now a light tint, no numeric floor); added the Figma per-element scope table; narrowed the 400–600 clamp to exclude `background.*-primary` and cover `background.*-solid` instead.

### v07

Packaged as a portable bundle (`SKILL.md` + `reference/`); `Global` collection marked hidden-from-publishing.

### v06

Split Phase 4 into reference files (`color.md`, `typography.md`, `spacing-radius-elevation-borderwidth-focusring.md`, `html-preview-structure.md`); added `figma-variable-structure.md` (3 collections — `Global`/`Color`/`Foundation`, 400–600 `-primary` clamp) and `figma-page-structure.md` (page skeleton); added the project-name onboarding question; dropped the confirmation-phrase gate.

### v01–v05

Locked the naming convention, W3C DTCG format, and the `[element].[role]-[emphasis](-[state])` semantic color pattern; gave every family a two-tier alias chain; added the mandatory HTML preview and the Figma Variables follow-up.
