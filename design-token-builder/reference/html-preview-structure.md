# Reference: HTML Preview Structure

Load this file in full before generating the token preview HTML. It documents the current, fully-evolved preview shell — build new previews by matching this structure, not by reinventing it.

---
## Overall shell

* Single self-contained HTML file. Token JSON is embedded as a `__TOKENS_JSON__` placeholder string, parsed once into a JS object at load.
* A `resolve(val, d, depth)` function recursively walks `{a.b.c}`-style alias strings against the parsed token tree and returns the final literal value (hex, px, number, etc.) — every renderer calls this rather than re-implementing alias resolution.
  * **Path lookup must be greedy longest-first, not a plain `split('.')`.** `border.global.border-0.5` carries a literal decimal inside its own key (the documented exception in `spacing-radius-elevation-borderwidth-focusring.md`), so splitting on every dot yields `border-0` + `5` and the lookup fails silently — `border.semantic.sm` resolves to `null` and the Border section renders empty. At each level, try the longest run of remaining segments that exists as a key on the current node, then fall back to shorter runs. Verify this one token renders before shipping the preview; it is the only alias in the system that catches a naive resolver.
* Layout: a sticky `<header>` at the top, then a `.layout` flex row containing `#sidebar` (left nav) and `main#app` (content).

## Typeface of the preview shell

* **The preview's own UI font is the token set's font family, not a system stack.** Read `typography.global.font-family-*` and use its value as the first entry of the page's `--sans` custom property; **`Noto Sans Thai` is the default**, matching `SKILL.md` Phase 2d's default answer, and it applies to the whole shell — headers, sidebar, section titles, table text — not only the Typography specimens.
* Load it from Google Fonts (`https://fonts.googleapis.com` + `https://fonts.gstatic.com`), requesting the weights the token set actually uses (on a default run, `400;700`), and always follow it with a real fallback stack: `"Noto Sans Thai", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`. The page must still read correctly if the font request fails.
* Monospace text (token keys, alias paths, hex values) keeps its own `--mono` stack — the project font applies to prose and UI chrome, not to the code-style columns, where a fixed-width face is what makes the alias paths scannable.

## Header (sticky)

* `position: sticky; top: 0; z-index: 20` — stays pinned during scroll, never scrolls out of view.
* On load and on window `resize`, `syncHeaderHeight()` measures `header.offsetHeight` and writes it into a `--header-h` CSS custom property on the root, so the sidebar can offset itself exactly below the header at any header height (e.g. if it wraps to two lines on narrow viewports).

## Sidebar (`#sidebar`)

* Sticky: `position: sticky; top: var(--header-h, 0px); height: calc(100vh - var(--header-h, 0px))`.
* Section links, in this fixed order: **Color, Typography, Spacing, Radius, Elevation, Border, Focus ring.** (No "Components" link or section — deliberately removed.)
* `initSidebar()` (trailing IIFE) wires an `IntersectionObserver` over each section's heading to toggle `.active` on the matching sidebar link as the user scrolls — scroll-spy behavior.
* **Click sets `.active` directly, not via the observer.** On click, move `.active` to the clicked link immediately, then scroll to its section. Raise a suppression flag for the duration of the smooth scroll (clear it on `scrollend`, with a ~600ms timeout fallback for browsers without `scrollend`) and have the observer skip its update while the flag is set — otherwise the sections sweeping past the trigger line during the scroll animation steal `.active` back from the link the user just clicked.
* **Edge override for the first and last links.** On `scroll`, when `window.innerHeight + window.scrollY >= document.body.scrollHeight - 2`, force `.active` onto the last sidebar link (Focus ring) regardless of what the observer reports; when `window.scrollY <= 2`, force it onto the first link (Color). Without this the last section can never activate, because the document runs out of scroll before its heading reaches the trigger line.
* **Bottom padding on `main#app` of at least `100vh`** (e.g. `padding-bottom: max(120px, 100vh)`), so every section — the last one included — can physically scroll up into the detection band. The edge override above handles the bottom of the document; this padding is what lets the observer itself reach the final section at all.
* Track the set of currently-intersecting sections and activate the **topmost** one, rather than whichever entry fired last — otherwise fast scrolling can leave a lower section highlighted while a higher one fills the viewport.

## Section: Color

* Two top-level tabs: **Global** and **Semantic**.
* **Global tab:** one strip per palette family **present in the token tree** (the per-family scale layout — same visual pattern as Typography's global scale, per explicit alignment request), each swatch showing the resolved hex and the token name. Derive the strip list by iterating `color.global` itself, never from a hardcoded eight-palette array — a project whose user answered *ไม่มี / None* to Secondary or Accent has no such branch, and the preview must simply not render that strip (no empty strip, no "not defined" note).
* **Semantic tab:** calls `renderSemanticTable()`, producing one `.sem-table` per **element** (`background`, `content`, `border`, `overlay`, `focus_ring` — no "Semantic Colors" headline wrapper above them; the element names themselves are the section headings, per explicit request to drop the redundant headline). Each table has columns **Name / Mode = Light / Mode = Dark / Description**:
  * **Name** — the full semantic key (`base-primary`, etc.).
  * **Mode = Light** — a color swatch, the raw `{alias}` path as literal text, and the resolved hex value.
  * **Mode = Dark** — the same swatch + alias + hex triple resolved against `color.semantic.dark`, when the run generated a dark theme; a literal `-` placeholder when it didn't.
  * **The Dark column carries its own dark ground.** Give that column's `<th>` and every `<td>` in it a near-black background (≈`#111114`), with the alias/hex text and the swatch border lightened to sit on it. Without this the dark-theme swatches — which are by definition near-black — render on the page's white surface and read as invisible or as mistakes; on their own dark ground they read the way they actually will in the product. Leave the Name, Light, and Description columns on the page surface.
  * **The band must be one unbroken panel, header included.** This is fiddly enough to get wrong that the details are locked here:
    * The table needs `border-collapse: separate; border-spacing: 0`. With `collapse`, cell `border-radius` is unreliable and the panel's corners render as square or clipped.
    * **The header cell owns the top rounding** (`border-top-left/right-radius`), and the **last body row owns the bottom rounding** — never put the top rounding on the first body row, which leaves a visible notch between the header and the table body and reads as two stacked blocks.
    * Give the `<th>` symmetrical vertical padding (≈`11px` top and bottom, not the `0`-top / small-bottom default a compact table header usually carries) so the dark ground doesn't hug the label's cap-height; and give the last row matching bottom padding so its rounded corner has room.
    * Every horizontal rule crossing the band — the header's `border-bottom`, each row's `border-bottom`, and the role-group separator — must switch to a **dark** hairline (≈`#23232b`) inside the Dark column. A light rule carried over from the surrounding table slices the panel into what look like separate cards.
    * Pad the Dark column's cells on **both** sides (≈`16px`), so the swatch isn't flush against the panel edge.
  * **Description** — a literal `-` placeholder (no description data generated yet).
  * **Rows come from the token tree, not a fixed roster:** iterate the keys actually present under each element, so a project without a Secondary/Accent palette renders fewer rows with no gaps or placeholders.
  * **Row order inside `background`:** per role, `primary → secondary → tertiary → quaternary → solid`. Read down that run and the swatches must go **lightest → deeper**, with the one dark `-solid` swatch last — this table is the fastest visual check that `color.md`'s inverted background direction was applied. `base` leads with its `base-plain` row (`#FFFFFF` Light / `#000000` Dark).

## Section: Typography

* One top-level tab per semantic weight level in the token set, labelled with the ladder name, thinnest → heaviest (`Lightest`, `Lighter`, `Light`, `Default`, `Strong`, `Stronger`, `Strongest`). On a default run that's exactly two tabs: **Default** and **Strong** — build the tab row from the token names actually present, never from a hardcoded `Regular`/`Bold` pair.
* Each row's label shows the full semantic key (`headline-md-strong`) with the resolved numeric weight (`700`) beside it, so the ladder name and the real weight are both legible.
* Within each tab, sections render **Headline** then **Body**, and within each family, steps are ordered **largest → smallest** (via a `rank()` sort function) — i.e. Headline `2xl → xs`, then Body `xl → xs`.
* No Display/Title/Label sections (those families don't exist in the token set — see `typography.md`).

## Section: Spacing

* A pill-selector row (`.pill-row` of `.pill` buttons, one per semantic step) above a live demo — 12 pills, `none` → `7xl`.
* Clicking a pill updates the demo's CSS `gap` in real time via a `.gap-demo` element, so the user can visually simulate each spacing step rather than only reading a swatch/number.
* Default the selection to **`md` (8px)**, so the preview opens on the anchor step.

## Section: Radius

* Same pill-selector pattern as Spacing, but driving a single `.sim-box` demo element's `border-radius` — 12 pills, `none` → `6xl`, then `full`.
* Box size is computed per selected step via `boxSizeFor(radiusPx) = Math.min(240, Math.max(96, radiusPx*3+40))` so large radii (e.g. `4xl`+) remain visually legible against a proportionally larger box, rather than the radius overwhelming a fixed small box.
* `full` (999px) is special-cased to a `160×56` pill-shaped box rather than running through `boxSizeFor`, since a square box can't demonstrate a pill radius — and `999` would blow past the `boxSizeFor` clamp anyway.
* Default the selection to **`md` (8px)**, matching Spacing.

## Sections: Elevation, Border, Focus ring

* All three share one layout pattern: a vertical list (`.vlist` containing `.vlist-row` items), each row a demo box paired with **left-aligned** stacked text (`.vlist-text` wrapping `.vlist-name` + value), separated by hairline dividers — no horizontal/grid layout for these three.

## What's deliberately absent

* No Components/`color.component` preview section — removed entirely per explicit request; the preview only covers Tier 1/Tier 2 (`global`/`semantic`) for every branch.

## Self-check before output (run silently)
* Header is `position: sticky`, `--header-h` is measured and reapplied on resize.
* The shell's `--sans` starts with the token set's own font family (`Noto Sans Thai` by default), the Google Fonts link requests the weights in use, and a system fallback stack follows it.
* Sidebar link order matches exactly: Color, Typography, Spacing, Radius, Elevation, Border, Focus ring — with scroll-spy `.active` toggling.
* Clicking any sidebar link — including the last one, Focus ring — leaves that link `.active` after the scroll settles, and scrolling to the very bottom of the page activates Focus ring on its own. This is the one scroll-spy case that fails silently.
* Color has its Global/Semantic two-tab structure; Semantic color tables have no extraneous "Semantic Colors" headline.
* Typography's tabs are derived from the weight levels present in the token set (`Default`/`Strong` on a default run) — zero hardcoded `Regular`/`Bold` labels anywhere in the markup or JS.
* In the `background` table, each role's `primary` swatch is visibly lighter than its `secondary`, and its `-solid` swatch is the darkest of the run.
* `background.base-plain` renders `#FFFFFF` in the Light column (and `#000000` in Dark, when a dark theme exists) — and its `#000000` swatch is actually visible, because the Dark column has its own dark ground.
* The Dark column reads as one continuous near-black panel from the header cell through the last row — rounded at the top of the `<th>` and the bottom of the final row, with no seam or notch under the header and no light hairline cutting across it. Check this visually per element table, not just on `background`.
* Every palette strip and every semantic row is derived by iterating the parsed token tree — searching the output for a palette the user answered *None* to returns nothing, and no section renders an empty container in its place.
* Spacing and Radius both use pill-selectors driving a live simulation, not static swatches, and both open on `md` = 8px.
* Elevation/Border/Focus ring use the shared left-aligned `.vlist` vertical pattern, and **`border.semantic.sm` shows `0.5px`, not `null`** — the dotted-key resolver check.
* No Components section anywhere in the output.
