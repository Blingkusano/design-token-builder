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
* Row counts per element (see `figma-variable-structure.md`): background **32**, content **28**, border 15, overlay 1, focus_ring 4 — fewer when the project has no Secondary and/or Accent palette, per `color.md`'s conditional-roles table. A removed role gets **no row at all** — not an empty row, not a "—" row.
* Within `background`, order rows per role as `primary → secondary → tertiary → quaternary → solid`, so the light-to-deep emphasis run and the single dark `-solid` step read in that order down the table. `base` additionally carries a `base-plain` row — place it first in the `base` group, since it's the plainest surface (`#FFFFFF`).
* Table columns: **Name** | **Value** | **Description** — one value column, because the `Color` collection has a single mode.
  * **Name** — the role/emphasis key (e.g. `base-primary`, `brand-primary`).
  * **Value** — a small color swatch plus the resolved hex, both driven by that `Color` variable's `Value` mode.
  * **Description** — no description data exists yet; fill with a literal `-` for every row.

**Section 2 — "Global Colors":**
* A second Figma Section, positioned below Section 1 as a sibling (not nested inside it) — titled "Global Colors".
* One table with only two columns: **Name** | **Hex Code** — one row per `Global` `color/*` primitive (every supplied palette × 11 steps, plus white/black/alpha scales), grouped visually by palette. A palette the user answered *ไม่มี / None* to has no group here, since it has no primitives. This is the only page that also exposes the raw Global primitives directly, since the reference layout this pattern is based on doesn't otherwise have a primitives view.
* **Note on this table vs. `Global`'s publishing visibility:** this table is an in-file reference for the `Global` variables' resolved values — a documentation table living on a canvas page inside this file, not a published/bound variable exposed to other files. It has no relationship to `figma-variable-structure.md`'s "Hidden from publishing" rule for the `Global` collection: that rule only controls whether a *different* file, subscribing to this one as a library, can bind to `Global` variables directly — it says nothing about what's rendered on this file's own pages. Build this table exactly as specified above regardless of `Global`'s publishing state, and don't write any of this explanation onto the actual Figma page — it exists here purely so this design intent isn't misread as a contradiction (e.g. during a skill audit).

## Sub-page: `  --> Typography`

* Small badge label "Style" at the top, then a title "Typography".
* One table, columns: **Name** | **Preview** | **Font Weight** | **Font Size** | **Line Height** | **Usage**.
  * **Name** — the `typography.semantic` key, using the semantic weight ladder (e.g. `headline-xs-default`, `body-lg-strong`) — never `-regular`/`-bold`.
  * **Preview** — a text sample rendered live in that style (apply the matching Text Style / bind fontSize+fontWeight+lineHeight to the `Foundation` `typography/[name]/*` variables).
  * **Font Weight** — show the resolved numeric weight (`400`, `700`) read from the bound `Global` variable, so the ladder name in the Name column and the real weight are both visible on the same row.
  * **Font Size** / **Line Height** — read from the same bound variables, shown as plain values.
  * **Usage** — no usage data exists yet; fill with a literal `-` for every row.
* Group rows under two bold section labels, in this order: **Headline** (6 sizes × weight levels, largest to smallest) then **Body** (5 sizes × weight levels, largest to smallest). On a default run that's 12 + 10 = 22 rows (`default` and `strong` only).

## Sub-page: `  --> Spacing`

* Small badge label "Variables" at the top, then a title "Spacing".
* One simple table, columns: **Name** | **Value** | **Global variable**.
  * **Name** — the `spacing.semantic` key (e.g. `md`).
  * **Value** — the resolved px number.
  * **Global variable** — the `Global` variable path it aliases (e.g. `spacing/space-8`).
* One row per `Foundation` `spacing/*` variable (12 rows: `none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, `7xl`), ordered smallest to largest. The `md` row must read **8 / `spacing/space-8`**.

## Sub-page: `  --> Radius`

* Same layout as Spacing: badge "Variables", title "Radius", table **Name** | **Value** | **Global variable**.
* One row per `Foundation` `radius/*` variable (**12 rows**: `none`, `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`, `4xl`, `5xl`, `6xl`, then `full` last), ordered smallest to largest with `full` at the end. The `md` row must read **8 / `radius/radius-8`**, and the `full` row **999 / `radius/radius-999`** — there is no `radius/radius-full` variable any more.

## Sub-page: `  --> Shadow`

* Small badge label "Style" at the top, then a title "Shadows".
* A card grid (not a table) — one card per `elevation.semantic` step (`none`, `sm`, `md`, `lg`), each card showing the step name as a label and a demo box beneath it with that step's shadow actually applied (bind the box's effect to the matching `elevation.global.shadow-*` value; `none` renders with no visible shadow — still show its card for completeness).

## Sub-page: `  --> Focus ring`

* Small badge label "Style" at the top, then a title "Focus rings".
* A row of cards, one per `color.semantic.focus_ring` role (`brand-primary`, `brand-secondary`, `error-primary`, `error-secondary`), each card showing: the role name, a colored ring/border around the card itself bound to that focus ring variable, and the resolved hex beneath the name.

## Plugin API constraints when building these pages (measured — not in Figma's own docs)

Every table on these pages is auto-layout rows of fixed-width cells, and that shape walks straight into three traps. Each one renders as a plausible-looking but wrong page, so write code that already avoids them.

* **`resize()` sets BOTH sizing modes to `FIXED`.** The natural way to build a fixed-width cell — `const c = figma.createAutoLayout('HORIZONTAL'); c.resize(w, 1); c.appendChild(text); c.resize(w, c.height)` — locks the height at `1px` on the first `resize`, so appending children never grows it and `c.height` is still `1` on the second. Every cell ends up 1px tall and the table renders as hairlines. **Fix: resize first, then re-open the axis you want hugging** — `c.resize(w, 1); c.counterAxisSizingMode = 'AUTO'; c.appendChild(text)`. The same trap hits any card built with `resize(w, h)` + `primaryAxisSizingMode = 'AUTO'`: set the AUTO axis *after* the resize, never before.
* **`figma.createAutoLayout()` returns a frame with an opaque white fill.** Wrapper rows, cells and grids that should be transparent therefore carry a hardcoded `#FFFFFF`, which Phase 8b counts as a binding violation across hundreds of nodes. **Fix: set `fills = []` on every structural wrapper at creation time** — only nodes that are deliberately a surface get a bound fill.
* **`strokeAlign = 'OUTSIDE'` gets clipped by an auto-layout parent.** A card whose ring sits outside its bounds loses the top and bottom runs of that ring, because the parent row hugs to the card's height and clips what spills past it — leaving what look like two vertical brackets. **Fix: use `strokeAlign = 'INSIDE'` for any ring or outline demo**, and set the four `stroke*Weight` properties explicitly rather than relying on `strokeWeight` alone.

**Verify these with returned geometry, not with a screenshot.** All three produce pages that are structurally present and will pass a node-count check, so `return` the actual numbers — `{name, x, y, width, height, counterAxisSizingMode}` for a sample row and cell — and read them before moving on. Save screenshots for the one visual confirmation per page at the end.

---
## Self-check before finishing Phase 7
* Page names match the skeleton above exactly, including the `  --> ` prefix on every Foundation sub-page and the all-caps `FOUNDATION` divider page.
* No "Getting Started" page exists, and no new `COMPONENT` page was created.
* Every `  --> Color` table has exactly three columns — Name / Value / Description — with no Light/Dark column pair.
* Cover page's background and text color are both variable-bound (never a literal fill), and the text reads the project name gathered in Phase 2 — not a placeholder.
* Every value shown on every Foundation sub-page traces back to a `Global`/`Color`/`Foundation` variable created by this skill — nothing was copied from the layout reference used to design these pages, and this file contains no link back to that reference.
* The "Global Colors" table on `  --> Color` is present and unaffected by `Global`'s hidden-from-publishing state.
* The `  --> Color` page has two separate top-level Figma Sections — "Semantic Colors" and "Global Colors" — as sibling Sections, not one blended layout or a nested Section.
* The `background` table has 32 rows including `base-plain`, `content` has 28 rows including the four `brand_secondary-*` rows (or the reduced counts from `color.md`'s conditional-roles table when an optional palette is absent), and every `background` role's rows run `primary → … → quaternary → solid` with `primary` visibly the lightest.
* No page anywhere shows a swatch, row, or group for a palette the user answered *None* to.
* The Typography table's Name column uses the semantic weight ladder (`-default`/`-strong`), and its Font Weight column shows the numeric weight.
* The Spacing and Radius tables both show `md` = **8**, and the Radius table has 12 rows ending in `full` = 999.
* Every table cell hugs its content vertically — sample one `Row` and one cell and confirm `height > 1`; a 1px cell means `resize()` locked the sizing mode.
* Every structural wrapper (rows, cells, grids) has `fills = []`; only deliberate surfaces carry a bound fill.
