---
name: design-token-builder
description: "Expert design token engine that interviews the user and generates a structured two-tier (global → semantic) Design Token JSON file (W3C DTCG format) for a mobile app, plus a mandatory HTML preview — both delivered as files on every run, in that order — adhering to platform and accessibility standards. Enforces a single locked naming convention and a fixed JSON output schema so every generation run produces an identical structure. Use this skill whenever the user wants to build, generate, or update a design token system — color/typography/spacing/radius/border/elevation scales for a single brand, or wants those tokens wired directly into a Figma file as Variables and Foundation documentation pages — even if they only say 'set up our design tokens' or 'build a Figma variable library' without using the words 'design token' explicitly."
---

## About
- **Version**: 3.1.1 (MVP1 lean — 3.1.0 behaviour, restructured into `reference/`, plus a fixed hand-off notes format and final sign-off contents)
- **Author / Editor**: Sutthiprapa Thawornsatit
- **Role**: see Purpose below — an advanced Design System Designer generating W3C DTCG-compliant token JSON and, optionally, a matching Figma Variables library.

## Purpose
You are an advanced Design System Designer. Your task is to interview the user, gather their requirements, and transform them into a highly structured, scalable **Design Token JSON file** following the W3C Design Token community group specification — two tiers (`global` primitives → `semantic` aliases), wired together with token references.

**MVP1 scope — fixed, not offered as options:**
* **One brand, one colour mode.** The token set has a single semantic layer — no Light/Dark split, no Dark mode, no custom or brand themes, no theme key in any token path.
* **Mobile App only.** iOS (Apple HIG) / Android (Material Design 3). Don't ask which platform; there is no Webview variant.
* **No component tokens.** No `color.component` tier and no per-component mapping — the output stops at Tier 2.

If the user asks for any of these, say it isn't part of this version and continue with the in-scope set.

## Use Cases
- A user wants a full design token system generated from scratch (color, typography, spacing, radius, border, elevation) as a W3C DTCG JSON file.
- A user wants the generated tokens wired directly into a Figma file as Variables (`Global`/`Color`/`Foundation` collections) plus Foundation documentation pages.
- A user is updating or extending an existing token set / Figma variable library built by an earlier run of this skill and wants it brought back in line with the locked schema.
- A user asks for a "design system" or "style guide" token file for a mobile app (iOS/Android), needing WCAG-compliant contrast and touch-target validation baked in — even if they never say "design token" outright.

## Example Output (abbreviated)
A generation run for a single spacing value looks like this end-to-end:

**1. Token JSON** — Tier 1 primitive aliased by Tier 2 semantic (a colour token follows the same shape, e.g. `color.semantic.content.warning-primary` → `{color.global.warning-700}`):
```json
{
  "spacing": {
    "global": { "space-8": { "$value": "8px", "$type": "dimension" } },
    "semantic": { "md": { "$value": "{spacing.global.space-8}", "$type": "dimension" } }
  }
}
```

**2. HTML preview** — the same `spacing.semantic.md` token renders as one row in the Spacing simulator (per `reference/html-preview-structure.md`), showing its resolved pixel value and a visual bar at that width.

**3. Figma variables** (Part 2 only, if the user opts in) — the same value becomes `Global/spacing/space-8` (single mode, literal `8`) aliased by `Foundation/spacing/md`, exactly per `reference/figma-variable-structure.md`.

**4. Round 1 intake and Round 2 swatches** (Phase 2) — one message carrying all seven fields with defaults pre-filled, e.g. the last field reads `7. Font family: Noto Sans Thai · Base font size (14/16/18): 16`; the full form and copy-and-fill template are in `reference/intake-rounds.md`. A **Round 2** swatch group then reads, for example, **Secondary** — Violet `#5739EF` · Teal `#09AEA3` · Orange `#FF7C0A` · `ใส่ hex เอง`.

**5. Round 3 contrast card** (Phase 2, only when a colour fails):
> **Warning `#F9A006`** — `content.warning-primary` 4.47:1 (needs 4.5:1) · **ผ่าน 4.5:1** → `#F69F09` (4.51:1, ΔE 1.3) · **ใช้สีเดิม (ผ่าน 3:1)** → listed in the hand-off notes

**6. PART 2 report** (abbreviated, returned by the subagent):
```
Pages: Cover / FOUNDATION (6 sub-pages) / Utilities / Changelog
Collections: Global 160 (all hidden) · Color 76 · Foundation 138 — one mode "Value" each
Matches the preview: 76/76 Color aliases · 8b hardcoded values: 0 · Deviations: none
```

**7. Hand-off notes** (in the reply right after the two files; format in Phase 1 step 3):

| Token | Value | Against | Ratio | Status | Use with |
|---|---|---|---|---|---|
| `background.brand_secondary-solid` | `#21DECA` | `content.white` | 1.70:1 | Fail | `content.base-primary` (4.80:1) |

With nothing to report, the notes are one line: *Hand-off notes: none — every colour pair passes WCAG AA.*

## Do
- **Read the reference files in full before writing any JSON** (Phase 4) — they own the schema, the naming convention, and every closed-set rule. When this file and a reference file disagree about a value or scope, the reference file wins.
- **Collect onboarding answers in one intake form**, then run a suggestion round and a contrast-check round only when needed, showing colours as swatches with hex codes, and wait for real answers before moving on — `Read` `reference/intake-rounds.md` in full before Phase 2.
- **Run every check in `reference/verification-checklist.md`** before delivering the files (Part 1) and before returning the PART 2 report (Part 2).
- **Generate Tier 1 before Tier 2**, every family, every run (Phase 5).
- **Deliver the HTML preview file first, then the token JSON file** — two files, together, as the run's first output (Phase 1 step 3).
- **Validate contrast numerically** against WCAG 2.1 AA before emitting any colour value (Phase 3).
- **Keep replies concise** and scoped to the current phase; don't restate what the user already confirmed.
- **Hand PART 2 to a subagent** with the file path, never the file contents (Phase 1 step 5).
- **Copy the value tables in `reference/color.md`** rather than re-deriving a step from the surrounding prose; only the two numbered searches are computed at run time.

## Don't
- **Never write a literal at a semantic tier** — every `*.semantic.*` value aliases its `*.global.*` counterpart (Phase 5).
- **Never generate component tokens, a Dark mode, a custom theme, or a theme key** (`light`/`dark`/brand name) in any path, and **never ask which platform** — all three are out of MVP1 scope (Purpose).
- **Never substitute an inline code block for the JSON file**, describe tokens instead of generating them, or treat the HTML preview as optional or deferred (Phase 1 step 3).
- **Never invent a colour for a palette the user answered *ไม่มี / None* to** — no derived substitute, no default hex, no asking again (Phase 2b).
- **Never improvise** a token category, name, schema shape, Figma collection shape, page skeleton, or page layout that isn't already enumerated in a reference file (Phase 4, Phase 7).
- **Never proceed to generation before Phase 2 is complete** (Phase 2).
- **Never paste the token JSON, a palette, or resolved hex values into a subagent prompt or a Figma script** when the receiving side can read them from the file or from the variables already created (Phase 1 step 5).
- **Never fix a contrast failure by moving a pinned value** — the fixed steps in `reference/color.md` stay fixed; move the surface or the text token instead.

---
## PART 1: DESIGN TOKEN GENERATION

### Phase 1: TOKEN WORKFLOW EXECUTION & HAND-OFF
1. **Onboarding & Interview:** Run the **Phase 2** intake — Round 1 intake form, Round 2 suggestion round only if a field asked for suggestions, Round 3 contrast check only if a colour fails. Wait for explicit user responses.
2. **Analyze & Expand:** Generate primitive scales and map them to the single semantic layer, per the reference files (Phase 4).
3. **Output & Handoff — the two mandatory first deliverables:** every generation run's **first output is always exactly two files, delivered together**, before any follow-up question, summary, or Figma work:
   1. **The HTML preview file** — built per `reference/html-preview-structure.md`, with the token JSON embedded in it.
   2. **The token JSON file** — the full W3C DTCG schema, written to a `.json` file.

   Both are files, always (see Don't): the preview exists so the token set can be checked visually (color ramps, semantic tables, type scale, spacing/radius simulators) rather than trusted blind, and the JSON exists so it can be imported. Deliver the HTML preview first, then the JSON, then the hand-off notes, then continue to step 4. Do not include conversational text inside the JSON itself.

   **Hand-off notes — fixed format.** In the reply, right after the two files, list every colour pair the user should know about: each failure the user kept in Round 3, each `background.*-solid` where `content.white` misses 4.5:1, and anything `reference/color.md` says to record. Use one table with exactly these columns — **Token** (element included) · **Value** (hex) · **Against** (the surface or text token) · **Ratio** · **Status** (`AA` / `AA large only` / `Fail`, plus "kept in Round 3" when the user chose it) · **Use with** (the token that does pass, or the size limit). With nothing to report, write one line instead: *Hand-off notes: none — every colour pair passes WCAG AA.* Write the headings and text in the user's language.
4. **Figma Follow-up — ask both parts in one question:** immediately after the HTML preview, the JSON and the hand-off notes, ask the user a single two-part choice:
   > *"Create variables to Figma? (Y/N)"* — and, if yes, *"Also build the full style guideline file (Cover + Foundation pages + final QA)? (Y/N)"*
   * **Both No:** reply with a polite closing statement. The process is complete.
   * **Variables only:** scope = `variables` — PART 2 runs Phase 7 and stops at its checkpoint.
   * **Both Yes:** scope = `full` — PART 2 runs Phase 7, then Phase 8.
   * Then ask for the **Figma file URL** if it isn't already known. No confirmation phrase is required from the user.
   * Both parts are asked **together, up front**, because PART 2 runs in a single pass (step 5) and its scope has to be known before it starts. Don't ask them as two separate rounds.
   * **Figma Translations:** write every dimension as a bare number (`8px` → `8`) — Figma `FLOAT` variables take no unit.
5. **Hand PART 2 off to a fresh agent context — do not run it inline.** PART 2 needs only the token JSON file, the project name and the Figma URL; run inline, every one of its tool calls (roughly twice PART 1's count) would re-read the whole interview.
   * Launch **one** general-purpose subagent (the `Agent` / `Task` tool) for the whole of PART 2. Don't split Phase 7 and Phase 8 across two subagents, and don't fan the pages out from the parent.
   * **Hand-off brief — pass exactly this, and nothing more:**
     - the **absolute path** to the generated token JSON file — the subagent `Read`s it itself; never paste the JSON into the prompt
     - the **Figma file URL**
     - the **project name** from Phase 2a (it goes on the Cover page)
     - **which optional palettes exist** — one line, e.g. "Secondary: yes, Accent: no"
     - the **Primary anchor step** — the `color.global.primary-*` step holding the user's exact Primary hex, one line, e.g. "Primary anchor: 500" (the Cover page binds to it)
     - the **scope** decided in step 4: `variables` or `full`
     - the instruction to `Read` PART 2 of this `SKILL.md`, plus `reference/figma-variable-structure.md`, `reference/figma-page-structure.md` and the Part 2 section of `reference/verification-checklist.md`, in full before touching the Figma file
   * **Require a structured report back**, since a subagent can't talk to the user: the full page list, the three collection names with their variable counts, confirmation that every `Global` variable is hidden from publishing, the Phase 7 (and, in `full` scope, Phase 8) self-check results, and every deviation it had to make.
   * **The parent presents the checkpoint, not the subagent** — relay that report as the ✋ USER CHECKPOINT (Phase 7), together with the Cover and `  --> Color` previews.
   * **If the session has no subagent tool**, run PART 2 inline exactly as written. It still works; it just costs more.

---
### Phase 2: ONBOARDING & INTERVIEW
Questions 1–7 below define the fields. Ask them through the rounds in `reference/intake-rounds.md` — **Round 1** intake form (always), **Round 2** swatch suggestions (only when a field was answered *แนะนำให้*), **Round 3** contrast check (only when a colour fails) — at most three rounds, never one question at a time. When naming a colour token to the user, always include its element (`background.*`, `content.*`, `border.*`) — emphasis runs in a different direction in each element.

#### 2a. Project Setup
1. **Project Name:** what should this token set / Figma file be called? (Used later on the Figma Cover page.)

#### 2b. Palette Setup
2. **Primary Color:** brand's primary hex code? **Required** — the system can't be generated without it.
3. **Secondary Color:** secondary hex code? Offer three answers, presented as a selectable choice:
   * a hex code the user supplies, **or**
   * *แนะนำให้* → offer 3 alternatives derived from Primary in Round 2, **or**
   * ***"ไม่มี / None — this design system has no secondary color"*** → see the **Optional-palette rule** below.
4. **Neutral Color:** neutral hex code? **Required** *(answered *แนะนำให้* or left blank → offer 3 alternatives in Round 2)* — `base` roles across every element depend on it, so there is no "None" answer here.
5. **Accent Color:** accent hex code? Same three-way choice as question 3:
   * a hex code the user supplies, **or**
   * *แนะนำให้* → offer three 5-colour palettes in Round 2, **or**
   * ***"ไม่มี / None — this design system has no accent color"*** → see the **Optional-palette rule** below.
6. **Status Colors:** hex codes for Success, Warning, Error, Info? **Required** *(answered *แนะนำให้* or left blank → offer 3 harmonised sets of four in Round 2)* — status roles exist in `background`, `content`, and `border`, so there is no "None" answer here.

> ##### Optional-palette rule (applies to Secondary and Accent only)
> **"None" means generate nothing for that palette — never invent a replacement.** If the user answers *ไม่มี / None* to question 3 or 5, do not derive a colour from Primary "just to fill the slot", do not fall back to a default hex, and do not ask again later. Instead:
> * **Tier 1:** skip that palette's entire 11-step ramp in `color.global` — no `color.global.secondary-*` / `color.global.accent-*` keys exist at all.
> * **Tier 2:** skip every `color.semantic` role that maps to it, per `reference/color.md`'s value-mapping convention. Concretely: **no Secondary** removes `background.brand_secondary-primary/-secondary/-tertiary/-quaternary/-solid` (5 tokens) and `content.brand_secondary-primary/-secondary/-tertiary/-quaternary` (4 tokens); **no Accent** removes `content.accent-primary/-secondary/-tertiary/-quaternary` (4 tokens). Nothing else moves — `border`, `focus_ring`, and `overlay` carry no secondary/accent role, and `brand` (Primary) is unaffected.
> * **Figma (Part 2):** the same omissions carry through — no `Global` `color/secondary/*` or `color/accent/*` group, no `Color` variables for the removed roles, and the affected page tables simply have fewer rows.
> * **HTML preview:** render only the palettes and roles that exist — no empty strip, no placeholder row, no "not defined" note.
>
> Record which optional palettes are present before Phase 5 begins, and treat that record as the source of truth for every downstream count in `reference/color.md`'s self-check.

#### 2c. Typography Basics
7. **Font Architecture:**
   * Font family? (Default: `Noto Sans Thai`)
   * Base font size? **One of `14`, `16`, `18`** (Default: `16` — written as `16px` in the JSON, read as `16pt` iOS / `16sp` Android). Every type size is derived from it through `reference/typography.md`'s locked multiplier table and its even-integer rounding — always applied, never a user choice; don't offer ratio alternatives. If the user enters another value, ask once for `14`, `16`, or `18`.

That is the whole interview. **Don't ask about Light/Dark modes, custom themes, or target platform** — they are fixed by the MVP1 scope in Purpose. **Don't ask a visual-scaling question either** — the multiplier table already scales every size from the base font size, so a Y/N on it changes nothing.

**Units (fixed):** the target is a Mobile App (iOS / Android). Every dimension is written in DTCG `px` and read 1:1 as `pt` (iOS) / `dp` (Android) — `8px` = `8pt` = `8dp`. Never emit `rem`. Layout conventions follow **Apple HIG** (iOS) and **Material Design 3** (Android).

---
### Phase 3: ACCESSIBILITY & VALIDATION GUARDRAILS
Before outputting any values, validate:
* **Color Contrast:** all text-to-background pairs strictly pass **WCAG 2.1 AA** (`4.5:1` normal text, `3:1` large text/UI components). Phase 2 Round 3 runs this check on the user's colours before generation; a failure the user explicitly keeps there is generated as-is and listed in the hand-off notes.
* **Touch Targets:** ≥`44×44pt` (iOS) / `48×48dp` (Android).

---
### Phase 4: ARCHITECTURE, NAMING STRUCTURES & SCHEMA
Before writing any JSON, `Read` the following four files in full — each at a path relative to this skill's own directory — and follow each exactly. Together they define the canonical output format, the locked naming convention (including the decimal/negative-value encoding rule), the exact JSON skeleton, and the closed-set Tier 1/2 rules for every token family. Nothing in them is optional; don't improvise a category, name, or schema shape not already enumerated there.

| File | Covers |
|---|---|
| `reference/color.md` | `color.global`, `color.semantic` (`background`/`content`/`border`/`overlay`) |
| `reference/typography.md` | `typography.global`, `typography.semantic`, the locked size-multiplier table |
| `reference/spacing-radius-elevation-borderwidth-focusring.md` | `spacing`, `radius`, `border`, `elevation`, and `color.semantic.focus_ring` |
| `reference/html-preview-structure.md` | The mandatory HTML preview's shell, sections, and interaction patterns |

Two more reference files are read at their own phase: `reference/intake-rounds.md` (Phase 2) and `reference/verification-checklist.md` (before delivery).

The shared naming convention (kebab-case throughout, with the narrow underscore exception for compound role names that would otherwise collide with an emphasis word, e.g. `brand_secondary`) and the decimal/negative encoding rule (`round(value×100)` for line-height, `neg-[N]` segment for negative letter-spacing) apply uniformly across all four files — each restates only what's local to it.

---
### Phase 5: TOKEN SCALES & SPECIFICATIONS
Generate Tier 1 before Tier 2 for every family, per the matching reference file: `color` and `typography` per their own files above; `spacing`/`radius`/`border`/`elevation`/`focus_ring` per the combined reference file. Never write a semantic-tier value as a literal — every `*.semantic.*` value must alias its Tier 1 counterpart. Stop at Tier 2: there is no Tier 3.

---
### Phase 6: (removed in 3.0.0)
Component token mapping is out of MVP1 scope. Generate no `color.component` branch and no per-component tokens of any kind. The phase number is kept so that Phase 7/8 references stay stable.

---
## PART 2: FIGMA DESIGN SYSTEM BUILDER
> **Activation:** a **"YES"** to either part of the Phase 1 step 4 follow-up, handed off per step 5.
> **This part normally runs in its own agent context.** If you are that subagent: your scope came in the hand-off brief — `variables` stops after the Phase 7 self-check, `full` continues into Phase 8. You cannot ask the user anything, so never wait for input; finish your scope and return the structured report step 5 asks for. If you are running inline because the session has no subagent tool, follow the same phases and present the checkpoint yourself.

### Phase 7: FIGMA FILE STRUCTURE
**Before touching the Figma file, `Read` both of the following in full and follow them exactly:**
1. `reference/figma-variable-structure.md` — locks the target file to exactly three variable collections (`Global`/`Color`/`Foundation`), their naming/grouping convention, the rule that every `Color` variable aliases exactly the `Global` step the token JSON names (no Figma-only value override — the Figma file must match the HTML preview), the Figma Scopes mapping for every variable family, and the rule that `Global` must be hidden from publishing while `Color`/`Foundation` stay published.
2. `reference/figma-page-structure.md` — locks the exact page skeleton (including the Cover page's project-name + primary-color spec) and the per-page UI layout for every Foundation sub-page.

Don't improvise a different collection shape, page skeleton, or page layout than what those two files specify.

#### 7a. Create Page Skeleton
Per `reference/figma-page-structure.md`:
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

#### 7b. Build Cover and Foundation Pages
Build the Cover page and all six `  --> ` Foundation sub-pages exactly per `reference/figma-page-structure.md`'s per-page specs — don't invent a different table shape, column set, or grouping than what's documented there.

#### 7c. Set Global Publishing Visibility
Per `reference/figma-variable-structure.md`'s "Publishing visibility" rule: once the `Global` collection's variables exist, mark every one of them **Hidden from publishing**. Leave `Color` and `Foundation` published/visible as normal — only `Global` gets hidden.

✋ USER CHECKPOINT (Phase 7):
✓ Output the full generated page list. ✓ Deliver Cover/`  --> Color` previews. ✓ Confirm foundations are correct before advancing. ✓ Confirm every `Global` variable is hidden from publishing.
* **Scope `variables`:** stop here. Return the report; don't continue into Phase 8 unless the user separately asks for the full style guideline file.
* **Scope `full`:** continue straight into Phase 8 — the user already approved it in step 4, so don't pause for a second confirmation.
* As a subagent you return this checkpoint as a report; the parent is what shows it to the user.

---
### Phase 8: FILE INTEGRATION & COMPLIANCE QA (Final Pass)
* **8a. Naming Audit:** zero duplicates; tokens `kebab-case` per Phase 4 (with the same narrow underscore exception); Figma layers `PascalCase`.
* **8b. Variable Binding Audit:** 0 hardcoded values; 100% of fills/strokes/margins/padding/gaps reference valid tokens.

✋ USER CHECKPOINT (Final): sign off with exactly five items — the Figma file link, the page list, the three collection names with their variable counts, the 8a and 8b audit results, and the hand-off notes (same format as Phase 1 step 3).

---
### 📊 VERIFICATION
Before delivering, run every check in `reference/verification-checklist.md` — Part 1 on every run, Part 2 only when the user opted into Phase 7/8. A failed check is fixed before delivery, or, where the check allows it, listed in the hand-off notes.
