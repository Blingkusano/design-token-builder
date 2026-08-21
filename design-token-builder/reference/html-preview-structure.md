# Reference: HTML Preview Structure

Load this file in full before generating the token preview HTML. It documents the current, fully-evolved preview shell — build new previews by matching this structure, not by reinventing it.

---
## Overall shell

* Single self-contained HTML file. Token JSON is embedded as a `__TOKENS_JSON__` placeholder string, parsed once into a JS object at load.
* A `resolve(val, d, depth)` function recursively walks `{a.b.c}`-style alias strings against the parsed token tree and returns the final literal value (hex, px, number, etc.) — every renderer calls this rather than re-implementing alias resolution.
  * **Path lookup must be greedy longest-first, not a plain `split('.')`.** `border.global.border-0.5` carries a literal decimal inside its own key (the documented exception in `spacing-radius-elevation-borderwidth-focusring.md`), so splitting on every dot yields `border-0` + `5` and the lookup fails silently, leaving the raw `{…}` string on screen. Try the longest joined segment first and fall back to shorter ones.
* Layout: a sticky `<header>` at the top, then a `.layout` flex row containing `#sidebar` (left nav) and `main#app` (content).

## Header (sticky)

* `position: sticky; top: 0; z-index: 20` — stays pinned during scroll, never scrolls out of view.
* On load and on window `resize`, `syncHeaderHeight()` measures `header.offsetHeight` and writes it into a `--header-h` CSS custom property on the root, so the sidebar can offset itself exactly below the header at any header height (e.g. if it wraps to two lines on narrow viewports).

## Sidebar (`#sidebar`)

* Sticky: `position: sticky; top: var(--header-h, 0px); height: calc(100vh - var(--header-h, 0px))`.
* Section links, in this fixed order: **Color, Typography, Spacing, Radius, Elevation, Border, Focus ring.** (No "Components" link or section — deliberately removed.)
* `initSidebar()` (trailing IIFE) wires an `IntersectionObserver` over each section's heading to toggle `.active` on the matching sidebar link as the user scrolls — scroll-spy behavior.
* **Click sets `.active` directly, not via the observer.** On click, move `.active` to the clicked link immediately, then scroll to its section. Raise a suppression flag for the duration of the smooth scroll (clear it on `scrollend`, with a ~600ms timeout fallback for browsers without `scrollend`) and have the observer skip its update while the flag is set — otherwise the sections sweeping past the trigger line during the scroll animation steal `.active` back from the link the user just clicked.
* **Edge override for the first and last links.** On `scroll`, when `window.innerHeight + window.scrollY >= document.body.scrollHeight - 2`, force `.active` onto the last sidebar link (Focus ring) regardless of what the observer reports; when `window.scrollY <= 2`, force it onto the first link (Color). Without this the last section can never activate, because the document runs out of scroll before its heading reaches the trigger line.

## Section: Color

* Two top-level tabs: **Global** and **Semantic**.
* **Global tab:** one strip per palette family (the per-family scale layout — same visual pattern as Typography's global scale, per explicit alignment request), each swatch showing the resolved hex and the token name.
* **Semantic tab:** calls `renderSemanticTable()`, producing one `.sem-table` per **element** (`background`, `content`, `border`, `overlay`, `focus_ring` — no "Semantic Colors" headline wrapper above them; the element names themselves are the section headings, per explicit request to drop the redundant headline). Each table has columns **Name / Mode = Light / Mode = Dark / Description**:
  * **Name** — the full semantic key (`base-primary`, etc.).
  * **Mode = Light** — a color swatch, the raw `{alias}` path as literal text, and the resolved hex value.
  * **Mode = Dark** and **Description** — both render as a literal `-` placeholder (no dark theme or description data generated yet).

## Section: Typography

* Two top-level tabs: **Regular** and **Bold** (matches the locked 2-weight semantic scale — no third tab).
* Within each tab, sections render **Headline** then **Body**, and within each family, steps are ordered **largest → smallest** (via a `rank()` sort function) — i.e. Headline `2xl → xs`, then Body `xl → xs`.
* No Display/Title/Label sections (those families don't exist in the token set — see `typography.md`).

## Section: Spacing

* A pill-selector row (`.pill-row` of `.pill` buttons, one per semantic step) above a live demo.
* Clicking a pill updates the demo's CSS `gap` in real time via a `.gap-demo` element, so the user can visually simulate each spacing step rather than only reading a swatch/number.

## Section: Radius

* Same pill-selector pattern as Spacing, but driving a single `.sim-box` demo element's `border-radius`.
* Box size is computed per selected step via `boxSizeFor(radiusPx) = Math.min(240, Math.max(96, radiusPx*3+40))` so large radii (e.g. `4xl`+) remain visually legible against a proportionally larger box, rather than the radius overwhelming a fixed small box.
* `full` is special-cased to a `160×56` pill-shaped box rather than running through `boxSizeFor`, since a square box can't demonstrate a pill radius.

## Sections: Elevation, Border, Focus ring

* All three share one layout pattern: a vertical list (`.vlist` containing `.vlist-row` items), each row a demo box paired with **left-aligned** stacked text (`.vlist-text` wrapping `.vlist-name` + value), separated by hairline dividers — no horizontal/grid layout for these three.

## What's deliberately absent

* No Components/`color.component` preview section — removed entirely per explicit request; the preview only covers Tier 1/Tier 2 (`global`/`semantic`) for every branch.

## Self-check before output (run silently)
* Header is `position: sticky`, `--header-h` is measured and reapplied on resize.
* Sidebar link order matches exactly: Color, Typography, Spacing, Radius, Elevation, Border, Focus ring — with scroll-spy `.active` toggling.
* Clicking any sidebar link — including the last one, Focus ring — leaves that link `.active` after the scroll settles, and scrolling to the very bottom of the page activates Focus ring on its own.
* Color/Typography each have their two-tab structure; Semantic color tables have no extraneous "Semantic Colors" headline.
* Spacing and Radius both use pill-selectors driving a live simulation, not static swatches.
* Elevation/Border/Focus ring use the shared left-aligned `.vlist` vertical pattern.
* No Components section anywhere in the output.
