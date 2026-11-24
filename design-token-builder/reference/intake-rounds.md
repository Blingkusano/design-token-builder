# Reference: Intake Rounds (Phase 2)

Load this file in full before starting `SKILL.md` Phase 2. `SKILL.md` Phase 2 defines the seven fields (questions 1–7); this file defines **how** they are asked: Round 1 always, Round 2 and Round 3 only when needed — at most three rounds. Name every colour token to the user with its element (`background.*`, `content.*`, `border.*`).

#### Round 1 — Intake form (always)
Present all seven questions at once, with defaults pre-filled, so the user answers in a single reply.
* **Where an inline form tool is available** (e.g. the Visualizer's elicitation form), render the intake as an interactive form: a text field for the project name; a hex field for Primary; a three-way choice (`ใส่ hex เอง` / `แนะนำให้` / `ไม่มี`) for Secondary and Accent; a two-way choice (`ใส่ hex เอง` / `แนะนำให้`) for Neutral; four Status hex fields plus a `แนะนำให้` option; font family pre-filled with its default; and base font size as a choice of `14` / `16` / `18`, with `16` pre-selected.
* **Otherwise**, post this template in a code block for the user to copy, fill in, and send back in one message:

```
1. ชื่อโปรเจกต์:

── สี ──
2. Primary (จำเป็น):           #
3. Secondary:                  #______  /  แนะนำให้  /  ไม่มี
4. Neutral (จำเป็น):            #______  /  แนะนำให้
5. Accent:                     #______  /  แนะนำให้  /  ไม่มี
6. Status (จำเป็น):             ใส่ hex ด้านล่าง  /  แนะนำให้
   - Success:                  #
   - Warning:                  #
   - Error:                    #
   - Info:                     #

── ตัวอักษร ──
7. Font family:                Noto Sans Thai
   Base font size (14/16/18):  16
```

* Under the form or template, add one short note each on: *ไม่มี* (nothing is generated for that palette), *แนะนำให้* (options are shown in the next round), Neutral and Status being required, and that every colour is contrast-checked before generation.
* **Parsing the reply:**
  * Font family left blank → its default. A blank Neutral counts as *แนะนำให้*.
  * Status answered *แนะนำให้*, or all four left blank → suggest all four in Round 2. Some given and some blank → suggest only the blank ones, harmonised with the hexes the user gave.
  * If Project name or Primary is missing, any hex is invalid, Secondary/Accent is left blank, or the base font size isn't 14, 16, or 18, ask for **only those fields, in one message**. Never re-ask a field that is already answered.

#### Round 2 — Suggestion round (only when needed)
Run this round only if Secondary, Accent, or Status was answered *แนะนำให้* (or Status was left wholly or partly blank), or Neutral was answered *แนะนำให้* / left blank. If every colour already has a hex or *ไม่มี*, skip to Round 3. Ask every triggered field **together, in one message**.
* **Show real colours, not just codes.** Every option is a colour box with its hex code underneath and a short name:
  * **Secondary** (derived from Primary) and **Neutral:** 3 single-colour cards.
  * **Accent:** 3 cards, each a 5-colour strip with all five hex codes listed below it.
  * **Status:** 3 cards, each a 4-colour strip labelled Success / Warning / Error / Info with the hex under each colour.
  * End each group with a `ใส่ hex เอง` option that reveals a hex field (four fields for Status), so the user isn't locked into the suggestions.
* **Every suggested colour must already pass Round 3's checks**, so a suggestion never reaches Round 3. For Status, also keep each hue in its conventional family (Success green, Warning amber, Error red, Info blue), match saturation and lightness to Primary so the four sit in one palette, and keep every status visibly distinct from Primary — if Primary sits within ~30° of a status hue, shift that status hue.
* **Where an inline form tool is available**, render this as a selectable swatch form (one choice group per triggered field, single-select). **Otherwise**, list each option as `name — #HEX` in plain text, and say that the HTML preview will show the colours as rendered.
* If the user skips the round or picks nothing for a field, ask once in plain text for that field. Never pick a colour on the user's behalf, and never read a skipped Secondary/Accent as *ไม่มี*.

#### Round 3 — Contrast check (only when a colour fails)
Once every colour is known, resolve these tokens with `reference/color.md`'s rules and check them against `background.base-primary` (step `50` of the chosen Neutral):
* **Text:** `content.[role]-primary` for `brand`, `brand_secondary`, `accent` (step `600`), `error` (`600`), and `warning` / `success` / `info` (`700`) — ≥ **4.5:1**.
* **Solid fills:** each `background.[role]-solid` — at least one of `content.white` or `content.base-primary` reaches ≥ **4.5:1** on it.

If everything passes, go straight to generation. Otherwise ask **once, in one message**, covering every failing colour. For each, show the current colour as a swatch with its hex and each failing token (element included) with its ratio, and offer:
* **ผ่าน 4.5:1** — the closest variant (same hue family, smallest visible change) whose checked tokens all reach 4.5:1.
* **ผ่าน 3:1** — the closest variant whose checked tokens reach 3:1 (large text and UI only). If the current colour already reaches 3:1, this choice is the current colour itself.
* **ใช้สีเดิม** — keep it: generate as-is and list each failing token and ratio in the hand-off notes.

Rules for every variant: run `color.md` on the variant and check the resulting tokens, never the hex alone; no resulting token value may equal a value another role already uses in the same element (`background`, `content`, `border`); `content` keeps `color.md`'s fixed emphasis order. If no variant in the same hue family reaches a threshold, say so on that card instead of offering it. Render the cards as a selectable swatch form where an inline form tool is available; otherwise list `name — #HEX — ratio`. Never pick for the user.

### Example — one Round 3 card (from a real run)

Primary `#066AFF`, Neutral `#677283`, Warning `#F9A006`. The ramp puts `content.warning-primary` at `warning-700` = `#9F6604`, which is 4.47:1 against `background.base-primary` (`#F6F7F8`), so Warning fails the 4.5:1 text check:

> **Warning `#F9A006`** — `content.warning-primary` 4.47:1 (needs 4.5:1)
> * **ผ่าน 4.5:1** → `#F69F09` — `content.warning-primary` 4.51:1, `background.warning-solid` 4.84:1 with `content.white` · ΔE 1.3 from the current colour
> * **ใช้สีเดิม (ผ่าน 3:1)** → `#F9A006` — the current colour already reaches 3:1, so the 3:1 choice and keeping it are the same card; the 4.47:1 is listed in the hand-off notes

A colour with no passing variant in its hue family shows that on its card instead of the missing option — e.g. **Secondary `#09AEA3`** — `content.brand_secondary-primary` 2.08:1: *no teal variant reaches 4.5:1* · **ผ่าน 3:1** → `#18A092` (3.02:1) · **ใช้สีเดิม**.
