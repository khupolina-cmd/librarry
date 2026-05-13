# Agent Instructions — librarry

This repository contains two local libraries and the skills that keep them stable:

- `font-library/` — HTML font library with live previews and `fonts.json` index
- `part_01_library_assets_and_manifests/` through `part_04_skills/` — reference image library and its skills

## Skills

All agent work in this repo must go through the skills in `part_04_skills/`.

| Skill | When to use |
|---|---|
| `font-library-indexer` | Adding a new font to the font library: accept font files + layer 1 user data, produce a complete `fonts.json` record |
| `reference-library-indexer` | Indexing reference images into the local searchable reference library |
| `reference-registry-batcher` | Building and updating the reference library registry from image batches |
| `vision-batch-indexer` | Batch-processing product or reference images into one structured card per file |

## Non-negotiable rules

- Do not bypass skills. Do not build ad hoc tools or one-off pipelines instead of using the skill.
- Do not hallucinate font metadata. If a field is not honestly known, write `unknown` or `not_collected`.
- Do not rewrite `fonts.json` from scratch. Only append or patch the relevant record.
- Do not rename stable vocabulary fields between sessions.
- When the user gives a font file path and a reason for saving it (layer 1), invoke `font-library-indexer`.
- When the user gives a folder of reference images, invoke `reference-library-indexer` or `reference-registry-batcher` depending on the task.
- When in doubt about which skill applies, read the `description:` header at the top of each `SKILL.md`.

## Repository layout

```
AGENTS.md                          ← this file
font-library/
  index.html                       ← HTML viewer (all CSS+JS inline)
  fonts.json                       ← master font index
  fonts/                           ← local font files per family
    <family-id>/
      *.woff2
part_01_library_assets_and_manifests/
part_02_library_cards_and_records/
part_03_context/
part_04_skills/
  font-library-indexer/            ← skill: add a font + index it
    SKILL.md
    evals/
    references/
  reference-library-indexer/
  reference-registry-batcher/
  vision-batch-indexer/
```

## Output files

- Font records go into `font-library/fonts.json`.
- Font files go into `font-library/fonts/<family-id>/`.
- Reference library outputs follow the schema defined in `reference-library-indexer`.
