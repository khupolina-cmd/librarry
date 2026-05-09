# Card shape

Each image card should be easy to read and easy to reuse later.

Use one file per image.

## Required top-level parts

- `asset`
- `batch_context_raw`
- `batch_context_normalized`
- `file_override`
- `observed`
- `quality`

## Add these when the project contract supports them

- `inferred`
- `style_mood`
- `applied_use_case`
- `normalized`
- `search`

## Numbering

Each card file name should use the manifest number:

- `001.json`
- `002.json`
- `003.json`

Also store the original file name inside `asset`.

## Tone

Keep the card technical and compact.

Good:

- separate fields
- short values
- review flags when needed

Bad:

- long captions
- decorative language
- repeating the same paragraph inside every section
