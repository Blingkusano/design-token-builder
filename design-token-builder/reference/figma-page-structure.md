# Reference: Figma Page Structure (Part 2 / Phase 7)

Load this file in full before building any page content in Phase 7. It defines the exact page skeleton, naming convention, and per-page UI layout to build — every layout pattern here was learned by inspecting a reference Figma file's structure (headers, table columns, grouping, card grids), **never** its actual token values. Every value that ends up on any page in this file comes from the `Global`/`Color`/`Foundation` variables this skill just created (per `figma-variable-structure.md`) — never a literal, never something copied from that reference file.

---
## Page skeleton (supersedes any earlier skeleton in `SKILL.md` Phase 7a)

```
Cover
---
FOUNDATION
  --> Color
  --> Typography
  --> Spacing
  --> Radius
  --> Shadow
  --> Focus ring
---
Utilities
Changelog
```

* No "Getting Started" page.
* `FOUNDATION` is a page name typed in **all caps**, exactly as shown — it acts as a section-divider page.
* Each Foundation sub-page name is literally `  --> {Name}` (two leading spaces, an arrow, a space, then the name) — e.g. the Figma page name is `  --> Color`, not `Color` or `Foundation / Color`. This mimics an indented sub-item look in Figma's flat page list.
* **v08 change:** the `COMPONENT` divider page and its per-component sub-pages are no longer part of this skill — building actual Figma components was removed in v08 (see `CHANGELOG.md`). Don't create a `COMPONENT` page even if an older project file already has one; leave any pre-existing `COMPONENT` page untouched rather than deleting it, since this skill no longer manages component pages either way.

## Cover page

* A single, otherwise-empty page/frame.
* Background fill: bound to the `Global` color variable that holds the user's **exact original Primary input hex** — this is the primitive at that palette's natural anchor step (see `color.md` Tier 1's anchoring rule: whichever step the input hex landed on when the ramp was generated, e.g. `color/primary/primary-600` if the input hex anchored at `600`). Bind it, don't hardcode the hex.
* One text layer, large and centered or top-left (match whatever reads cleanly against the primary fill): the **project name** the user gave in Phase 2's first question — this replaces any placeholder like "Token Builder". If the project name isn't accessible against the primary color as literal black/white, pick whichever of `color/white` or `color/black` (from `Global`) passes better contrast — still bind it as a variable, don't hardcode.

## Sub-page: `  --> Color`

* Small badge label reading "Style" at the top, then a title "Semantic Colors".
* Build this page as **two separate top-level Figma Sections**, stacked top to bottom, each its own Section node with its own title — don't let the Global table sit loose below the semantic tables on the same canvas area; the two must be structurally distinct Sections, not just visually separated by spacing.

**Section 1 — "Semantic Colors":**
* One sub-heading per `Color` collection element — `background`, `content`, `border`, `overlay`, `focus_ring` — each with a bold heading (the element name) followed by a table, all nested inside this one Section.
* Table columns: **Name** | **Light Value** | **Dark Value** | **Description**.
  * **Name** — the role/emphasis key (e.g. `base-primary`, `brand-primary`).
  * **Light Value** — a small color swatch plus the resolved hex, both driven by the `Light` mode of that `Color` variable.
  * **Dark Value** — same, driven by the `Dark` mode of the same variable.
  * **Description** — no description data exists yet; fill with a literal `-` for every row.

**Section 2 — "Global Colors":**
* A second Figma Section, positioned below Section 1 as a sibling (not nested inside it) — titled "Global Colors".
* One table with only two columns: **Name** | **Hex Code** — one row per `Global` `color/*` primitive (all 8 palettes × 11 steps, plus white/black/alpha scales), grouped visually by palette. This is the only page that also exposes the raw Global primitives directly, since the reference layout this pattern is based on doesn't otherwise have a primitives view.
* **Note on this table vs. `Global`'s publishing visibility:** this table is an in-file reference for the `Global` variables' resolved values — a documentation table living on a canvas page inside this file, not a published/bound variable exposed to other files. It has no relationship to `figma-variable-structure.md`'s "Hidden from publishing" rule for the `Global` collection: that rule only controls whether a *different* file, subscribing to this one as a library, can bind to `Global` variables directly — it says nothing about what's rendered on this file's own pages. Build this table exactly as specified above regardless of `Global`'s publishing state, and don't write any of this explanation onto the actual Figma page — it exists here purely so this design intent isn't misread as a contradiction (e.g. during a skill audit).

## Sub-page: `  --> Typography`

* Small badge label "Style" at the top, then a title "Typography".
* One table, columns: **Name** | **Preview** | **Font Weight** | **Font Size** | **Line Height** | **Usage**.
  * **Name** — the `typography.semantic` key (e.g. `headline-xs-regular`).
  * **Preview** — a text sample rendered live in that style (apply the matching Text Style / bind fontSize+fontWeight+lineHeight to the `Foundation` `typography/[name]/*` variables).
  * **Font Weight** / **Font Size** / **Line Height** — read from the same bound variables, shown as plain values.
  * **Usage** — no usage data exists yet; fill with a literal `-` for every row.
* Group rows under two bold section labels, in this order: **Headline** (6 sizes × 2 weights = 12 rows, largest to smallest) then **Body** (5 sizes × 2 weights = 10 rows, largest to smallest).

## Sub-page: `  --> Spacing`

* Small badge label "Variables" at the top, then a title "Spacing".
* One simple table, columns: **Name** | **Value** | **Global variable**.
  * **Name** — the `spacing.semantic` key (e.g. `md`).
  * **Value** — the resolved px number.
  * **Global variable** — the `Global` variable path it aliases (e.g. `spacing/space-16`).
* One row per `Foundation` `spacing/*` variable (12 rows), ordered smallest to largest.

## Sub-page: `  --> Radius`

* Same layout as Spacing: badge "Variables", title "Radius", table **Name** | **Value** | **Global variable**.
* One row per `Foundation` `radius/*` variable (13 rows, including `full`), ordered smallest to largest.

## Sub-page: `  --> Shadow`

* Small badge label "Style" at the top, then a title "Shadows".
* A card grid (not a table) — one card per `elevation.semantic` step (`none`, `sm`, `md`, `lg`), each card showing the step name as a label and a demo box beneath it with that step's shadow actually applied (bind the box's effect to the matching `elevation.global.shadow-*` value; `none` renders with no visible shadow — still show its card for completeness).

## Sub-page: `  --> Focus ring`

* Small badge label "Style" at the top, then a title "Focus rings".
* A row of cards, one per `color.semantic.[theme].focus_ring` role (`brand-primary`, `brand-secondary`, `error-primary`, `error-secondary`), each card showing: the role name, a colored ring/border around the card itself bound to that focus ring variable, and the resolved hex beneath the name.

## Self-check before finishing Phase 7
* Page names match the skeleton above exactly, including the `  --> ` prefix on every Foundation sub-page and the all-caps `FOUNDATION` divider page.
* No "Getting Started" page exists, and no new `COMPONENT` page was created.
* Cover page's background and text color are both variable-bound (never a literal fill), and the text reads the project name gathered in Phase 2 — not a placeholder.
* Every value shown on every Foundation sub-page traces back to a `Global`/`Color`/`Foundation` variable created by this skill — nothing was copied from the layout reference used to design these pages, and this file contains no link back to that reference.
* The "Global Colors" table on `  --> Color` is present and unaffected by `Global`'s hidden-from-publishing state.
* The `  --> Color` page has two separate top-level Figma Sections — "Semantic Colors" and "Global Colors" — as sibling Sections, not one blended layout or a nested Section.
