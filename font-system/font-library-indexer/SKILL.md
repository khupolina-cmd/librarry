---
name: font-library-indexer
description: Add a new font to the local font library. Use this skill whenever the user gives a font (local files or Google Fonts ID) plus any first layer of personal data about it — why they saved it, how they intend to use it, what character it has — and wants it indexed in font-library/fonts.json so it appears in the HTML viewer and can be retrieved by style, mood, function, or context. Also use it when the user wants to update or patch an existing font record.
---

# Font Library Indexer

Use this skill to turn one font entry — a typeface name, its files or CDN ID, and the user's first layer of intent — into a complete, retrieval-ready record inside `font-library/fonts.json`.

The point is not to generate a caption.
The point is not to make a standalone demo.
The point is to keep one stable indexing pipeline that feeds:

- the local `fonts.json` master index
- the HTML viewer and filter surface
- future tag-based and free-text retrieval

## Non-negotiable stance

Stay inside the skill contract.

- Do not invent metadata that was not given and cannot be inferred from the font files.
- Do not silently rename stable vocabulary fields.
- Do not flatten the record into a one-line label.
- Do not skip the `index` block just because the font feels obvious.
- If a field is not honestly known yet, write `"not_collected"` — never fabricate certainty.

## Layer model

This skill uses a two-layer input model:

### Layer 1 — user intent (mandatory)

Everything the user brings:

- font family name
- font file path(s) or Google Fonts ID
- why they saved this font
- how they plan to use it (context, project type, pairing intent)
- personal words they associate with the character

Layer 1 is user-owned. Do not overwrite it with Claude's interpretation.
If the user gives only a font name and no context, prompt for layer 1 before continuing.

### Layer 2 — structured technical fields (Claude fills)

Claude fills from the font files and established vocabulary:

- `id` — slugified family name, lowercase hyphen-separated
- `family` — display name of the font family
- `google_fonts_id` — Google Fonts API slug or `null`
- `google_fonts_variants` — variant query string for Google Fonts CSS2 API or `null`
- `local_path` — relative path from `font-library/` to the family folder
- `files` — array of file descriptors `{ file, weight, italic }` for every local woff2
- `default_weight` — the recommended starting weight (usually 400)
- `weight_range` — `[min, max]` from available weights
- `tags.style` — typographic style labels (see vocabulary)
- `tags.mood` — emotional/character labels in Russian
- `tags.function` — intended typographic roles
- `tags.context` — project/brand contexts
- `technical.has_cyrillic` — boolean
- `technical.is_variable` — boolean
- `technical.styles_count` — integer count of available styles
- `technical.license_status` — one of: `web_use_allowed`, `checked_private_local`, `unknown`
- `technical.license_note` — short license description
- `description` — one or two sentence character summary in Russian, grounded in observable properties
- `specimen.hero` — 2–3 word ALL-CAPS headline specimen, in Russian or Latin matching the font's character
- `specimen.poster` — 5–10 word evocative phrase, mixed case, in the language that fits the font
- `specimen.info` — short technical or packaging line in the font's register
- `index.style_label` — combined style descriptor for retrieval (3–5 words, English)
- `index.mood_label` — combined mood descriptor in Russian
- `index.function_label` — combined function descriptor in Russian
- `index.context_label` — combined context descriptor in English
- `index.strength` — one sentence saying what makes this font the right choice (Russian)

## Processing order

Always work in this order:

1. collect layer 1 from the user
2. locate the font files or confirm the Google Fonts CDN ID
3. inspect font metadata: weights, styles, variable axes, Cyrillic support, license
4. fill all layer 2 fields from evidence, not assumption
5. write `description` grounded in observable font properties
6. write `specimen` entries appropriate to the font's register
7. write `index` block for retrieval
8. append the completed record to `font-system/font-library/fonts.json`
9. if local files exist, confirm they are placed in `font-system/font-library/fonts/<id>/`
10. confirm the HTML viewer will pick up the new record on next load

## Vocabulary — `tags.style`

Use only approved terms. Do not coin new ones without flagging them for review.

- `serif`
- `sans-serif`
- `display`
- `monospace`
- `script`
- `handwritten`
- `geometric`
- `humanist`
- `grotesque`
- `transitional`
- `garalde`
- `didone`
- `slab-serif`
- `contemporary`
- `editorial`
- `fashion`
- `classic`
- `high-contrast`
- `variable`
- `old-style`
- `expressive`
- `bookface`
- `screen`
- `minimal`

If a new term is needed, flag it at the end of the output as `vocabulary_delta`.

## Vocabulary — `tags.function`

Approved terms:

- `headline`
- `display`
- `hero`
- `subheading`
- `body`
- `caption`
- `long-form`
- `packaging`
- `title`
- `fashion-editorial`
- `poster`

## Vocabulary — `license_status`

| Value | Meaning |
|---|---|
| `web_use_allowed` | OFL, Apache 2.0, or other free web license |
| `checked_private_local` | Desktop license — private local use only |
| `unknown` | License status not verified |

Always note the specific license name in `license_note`.

## Specimen writing rules

- `hero` must be short, uppercase-friendly, and demonstrate the font's strongest display register
- `poster` must be a phrase the font would carry on an actual poster or editorial layout
- `info` must be a neutral technical or product line — this tests legibility at small sizes
- Do not repeat the same text across hero / poster / info
- Prefer language (Russian or Latin) that matches the font's natural character

## Indexing rules

- `style_label` must be usable as a search query fragment in English (e.g. `"contemporary editorial serif"`)
- `mood_label` must be human-readable Russian, usable as a free-text query
- `function_label` must list primary use cases in Russian, comma-separated
- `context_label` must list brand/project types in English, comma-separated
- `strength` must be a concrete single sentence in Russian — what distinguishes this font from all others in the library

## Output contract

After processing, produce:

1. The complete new or updated JSON record (for review before writing)
2. Confirmation of where in `fonts.json` it was appended or patched
3. Path to font files (if local)
4. `vocabulary_delta` — any proposed new tags not in the approved list (empty if none)
5. `review_notes` — any fields that could not be filled with confidence (empty if none)

## What to avoid

- Do not ask the user to fill in technical fields — Claude fills layer 2
- Do not silently skip `description`, `specimen`, or `index` blocks
- Do not invent Cyrillic support when the font file was not inspected
- Do not guess the license — default to `unknown` until verified
- Do not open a fresh font schema each session — always append to the existing `fonts.json`
- Do not convert `fonts.json` into a flat list of names

## Reference files

Read these before starting:

- `font-system/font-library/fonts.json` — current master index (always load; shows existing records and vocabulary in use)
- `font-system/font-library/README.md` — font file layout and field documentation
- `font-system/font-library-indexer/references/font-record-shape.md` — canonical field reference

## Success condition

This skill is working when the user can say:

> "Вот шрифт X, я хочу использовать его для beauty-заголовков, он ощущается как дорогой и холодный."

and get back:

- a complete `fonts.json` record with all fields filled
- correct file paths
- retrieval-ready tags that let future queries like "холодный editorial serif" or "beauty headline" return this font
- the record visible in the HTML viewer on next load
