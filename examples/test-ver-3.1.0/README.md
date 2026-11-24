# Example run — Test ver 3.1.0

The regression run used to check 3.1.0. The answers were:

| Field | Value |
|---|---|
| Project | Test ver 3.1.0 |
| Primary | `#066AFF` |
| Secondary | `#18A092` (Round 2 suggested Teal `#09AEA3`; Round 3 picked the 3:1 variant) |
| Neutral | `#677283` (from Round 2) |
| Accent | none |
| Success | `#2CAF5A` (Round 3, 4.5:1 variant) |
| Warning | `#F69F09` (Round 3, 4.5:1 variant) |
| Error | `#E9202E` |
| Info | `#2ABBCB` (Round 3, 4.5:1 variant) |
| Font | Noto Sans Thai, base 16 |

## Expected results

**Counts:** `color.semantic` has background 32, content 24, border 15, focus_ring 4 and overlay 1.

**Contrast:**
- Every `content.[status]-primary` reaches 4.5:1 or more on `background.base-primary`.
- Every `background.*-solid` is between `400` and `700`, and has a text token that reaches 4.5:1.

**Known, accepted notes** (these go in the hand-off notes):
- `content.brand_secondary-primary` is 3.02:1. The user chose the 3:1 option.
- `background.brand_secondary-solid` (`#21DECA`) needs `content.base-primary` text, which gives 4.8:1.

**Figma:** the Figma file built from this JSON had 0 alias differences from the JSON, across 76 Color variables and 103 Global colours.
