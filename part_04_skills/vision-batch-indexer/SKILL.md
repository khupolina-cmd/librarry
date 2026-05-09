---
name: vision-batch-indexer
description: Process a prepared batch folder of product or reference images into one structured card per file. Use this skill whenever the user gives a folder of photos and wants Claude to inspect each image, fill technical fields, copy one batch-level note into every file, apply file-specific exceptions, and save clean outputs for future search. Also use it when the user speaks or pastes one common batch comment and wants that comment preserved inside every image record.
---

# Vision Batch Indexer

Use this skill for one simple job:

- take a prepared folder of images
- inspect each image
- build one structured record per file
- preserve the user's batch note inside every record
- mark exceptions without breaking the whole batch

This skill is not for poetic captions.
This skill is for stable batch processing.

## What this skill expects

The usual input is:

- a folder with images
- one common note for the whole folder
- optional file-specific exceptions

The common note can come in either form:

- pasted by the user in chat
- saved as `batch-note.md` inside the batch folder

File-specific exceptions can come in either form:

- pasted by the user in chat
- saved as `overrides.md` inside the batch folder

If both chat text and files exist, prefer the latest user instruction in chat.

## Numbering rule

Always create a simple batch manifest first.

- assign each image a stable number: `001`, `002`, `003`...
- save the mapping in `outputs/manifest.csv`
- use that number in every output file and every review note

Do not rename the original image files unless the user explicitly asks for that.

## What counts as batch note

Treat the batch note as shared context.

It should be copied into every image record in two forms:

- `batch_context_raw`
- `batch_context_normalized`

If the user says a point applies only to one image, do not spread that point to the whole batch.

## What counts as exception

Treat exceptions as stronger than the shared batch note.

If the user says:

- `#007 is different`
- `#013 is not about texture`
- `#021 is the key image`

apply that only to the named image numbers.

If the user does not name a file clearly, create a review note instead of guessing.

## What to produce

Inside the batch folder, write to `outputs/`:

1. `manifest.csv`
2. `batch_context.md`
3. `review.md`
4. `cards/<image-no>.json` for each image

Each card should include at least:

- asset info
- observed facts
- quality flags
- inferred scene layer
- applied use-case layer
- normalized terms
- search fields
- the shared batch note
- any file-specific override

## Processing order

Always work in this order:

1. find the batch folder
2. collect image files
3. create `manifest.csv`
4. load the batch note
5. load file-specific exceptions
6. normalize the shared note into a short clean batch context
7. inspect each image and fill the technical record
8. in the same per-image pass, build normalized search terms and semantic match families
9. attach the shared batch context to every image
10. apply file-specific overrides
11. mark uncertain cases in `review.md`

If the project already includes a stricter schema or prompt set, reuse it instead of inventing a new one.

## Semantic match rule

For this skill, semantic match is not a viewer-side patch.

It must be created during indexing.

That means:

- when the model inspects an image, it must capture every retrieval-relevant visible object, surface, effect, and interaction
- in the same image-processing run, those observations must be expanded into search-ready families and aliases
- Russian wording must be first-class, not an afterthought
- English variants may be stored too, but they do not replace Russian search hooks

Example:

- if the frame contains bathroom tile, the record must already carry the family needed for `плитка`, `кафель`, and `tile`
- if the frame contains a metal tray, the record must already carry the family needed for `поднос`, `металлический поднос`, and `tray`
- if the frame contains a cream drop on a nozzle, the record must already carry the family needed for `капля крема`, `капля продукта`, and `cream drop`

The viewer is allowed to read, filter, and rank these search artifacts.
It is not allowed to invent first-order semantic matches that should have been created at indexing time.

## Project contract for this workspace

When working inside the ARCHITECT project, use these files as the source of truth:

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/schemas/extraction_output.schema.json`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/classifiers/inferred_style.schema.json`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/classifiers/applied_use_case.schema.json`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/INDEX_SCHEMA.md`

If one of these files is missing, continue with the closest available contract and note the gap in `review.md`.

## Hard rules

- Do not replace structured fields with prose.
- Do not drop the user's batch note.
- Do not treat brand text as the meaning of the image.
- Do not invent certainty when the frame is ambiguous.
- Do not fork vocabulary casually.
- Do not ask the user to pre-fill technical fields manually.
- Do not postpone primary semantic matching to the HTML viewer layer.

## Review rules

Write a short `review.md` when:

- the batch note is too vague
- an exception is unclear
- a field is uncertain
- a new term may be needed
- one image clearly breaks the batch pattern

Keep review notes short and tied to image numbers.

## Helpful files

For input and output examples, read only what you need:

- `references/input-note-format.md`
- `references/card-shape.md`

## Success condition

This skill is working if the user can hand over:

- one image folder
- one shared note
- a few exceptions

and get back:

- one clean record per image
- stable numbering
- preserved batch context
- a short review list instead of chaos
