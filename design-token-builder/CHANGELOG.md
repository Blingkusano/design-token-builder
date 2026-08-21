# Changelog

All notable changes to this skill are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
