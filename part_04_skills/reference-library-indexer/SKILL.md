---
name: reference-library-indexer
description: Index a folder or batch of reference images into a local searchable library with one raw observation, one normalized card, and one search record per image. Use this skill whenever the user mentions a batch of photos, a folder of refs, a local reference library, indexing images, building a searchable image collection, adding new refs into an HTML library, or wants future search by filters and free text to work cleanly. This skill should also be used when the user wants every meaningful visible object in a frame preserved for retrieval, even if that object is secondary and not part of curated props.
---

# Reference Library Indexer

Use this skill to turn a batch of reference or product images into a **local-first searchable reference library**.

The point is not to generate captions.
The point is not to build a random standalone prototype.
The point is to keep one stable indexing pipeline that feeds:

- local library storage
- normalized retrieval
- HTML browsing / filtering
- future batch expansion without vocabulary drift

## Non-negotiable stance

Stay inside the skill-driven local workflow.

Image reading and indexing are executed locally by Codex inside this skill.
Do not assume or introduce any external vision API, remote model call, free model, or separate outsourced image-reading service.
If the user switches to a cheaper or smaller model, the responsibility still stays here: the same local skill reads the images and builds the records.

Do not quietly reframe the task into:

- a separate ad hoc tool
- a viewer-only demo
- a legacy batch adapter
- a one-off flat JSON dump with no library structure
- an external vision pipeline hidden behind the same interface

If implementation code is needed, it should serve the skill and the local library contract.

## Load these files first

Before doing batch work, read:

1. `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/implementer-handoff-letter-v1.md`
2. `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/implementer-package-index-v1.md`
3. `/Users/alittlepinkie/.codex/skills/reference-library-indexer/references/source-of-truth.md`

Then read only the specific architecture files needed for the current step.

## What this skill must produce

For every image in the batch:

1. `raw observation`
2. `normalized card`
3. `search record`

For the batch/library:

1. manifest
2. stable local storage
3. retrieval-ready search surface
4. HTML-visible browsing surface
5. review notes for unresolved or newly emerging cases

## Core rules

- Index **every image**.
- Preserve **every meaningful visible object** as searchable retrieval evidence, even if it is secondary.
- Keep `props` and `searchable_objects` separate.
- Keep `raw_observation`, `normalized_card`, `search_record`, and `ui_view` separate.
- Never let the HTML layer invent first-order semantics that should have been created at indexing time.
- If a field is not honestly collected yet, keep it `not_collected` instead of hallucinating certainty.
- Do not revive legacy labels like `minimal_luxury` or `glossy_commercial`.

## User-facing semantic rules that are already fixed

Do not reopen these casually:

- `source_title` брать только если он уже есть в имени / названии; ничего не додумывать
- если имя автора или студии неизвестно, писать `noname`
- `product_package_count_bucket` = `1 / 2 / 3 / 4 / 4+`
- active `crop_type` = `no_crop / loose_crop / tight_crop / partial_crop`
- if the product touches the frame edge, this is at least `tight_crop`
- if a significant part leaves the frame, this is `partial_crop`
- separate `shot scale` is removed from the active model for now
- `product_position` is removed from the active model for now
- support surfaces like tray/podium/special support break `Product-only`
- `tray`, `cutlery`, cups, glasses, and similar serving items collapse into one prop family: `tableware`
- multi-product frames must support `product_items`
- packaging helper subfields are parked for now:
  - `dispenser_type`
  - `body_rigidity`
  - `top_opening_type`
  - `shape_profile`
- `finish` is parked for now
- product category must stay high-level only:
  - `skincare`
  - `beauty`
  - `fragrance`
  - `bags`
  - `electronics`
- `Product + hand` is an active frame type and should be used when the hand is a meaningful scene component, not just an accidental crop edge
- all meaningful visible objects must be searchable
- if shadow is unclear, hard vs soft light should be resolved from highlight shape:
  - small bright point highlight => hard
  - broad / elongated highlight => soft
- `backlight` only when the product edge or rim is actually lit, not just because the background behind it is bright
- if the product bottom is visible, this is a strong signal toward `Low angle`
- if the top / cap is visible from above, this is a strong signal toward `Top view`
- `composition_density` should be read as text-overlay safety:
  - how much calm text-safe space remains
  - not just how many objects are present

## Default processing order

Always work in this order:

1. locate the batch or project folder
2. locate or create the local library root
3. create a stable manifest
4. inspect each image into `raw observation`
5. normalize each image into `normalized card`
6. project each card into `search record`
7. write all outputs into the library structure
8. rebuild or update the HTML browsing layer
9. write review notes for unresolved terms, conflicts, or low-confidence cases

## Expected inputs

Typical inputs:

- batch folder path
- project folder path
- existing library path
- optional batch note
- optional overrides

If the user gives only the folder, infer the rest from project context before asking questions.

Ask the user only about **photo semantics or panel meaning** when a real semantic gap blocks you.
Do not ask the user technical implementation questions if the contract already gives enough direction.

## Default library behavior

If the project already has a library structure, preserve it.
If not, use the default layout in:

- `/Users/alittlepinkie/.codex/skills/reference-library-indexer/references/library-layout.md`

## Retrieval expectations

The library must support:

- filters
- free text query
- multi-clause query
- combination query
- object retrieval
- prop retrieval
- count retrieval
- category co-presence retrieval

Examples of required behavior:

- if a cabbage is visible in the background, `капуста` must be able to retrieve that frame
- `2 упаковки` must rank count-correct frames above one-product frames
- `шампунь + бальзам` must prefer true co-presence, not loose coincidence
- `белый` and `вода` must not fail just because the query is simple human language

## Confidence and review

When the result will be saved into the library, apply a confidence gate.

Low confidence if any of these happen:

- required fields are empty
- conflicting frame logic
- count and frame type disagree
- new uncontrolled vocabulary appears
- important field is inferred without real visual evidence

When confidence is low:

- still preserve the image record
- mark review notes
- do not silently fake certainty

## Helpful references

Read these only when needed:

- `/Users/alittlepinkie/.codex/skills/reference-library-indexer/references/source-of-truth.md`
- `/Users/alittlepinkie/.codex/skills/reference-library-indexer/references/library-layout.md`

## Success condition

This skill is working when the user can hand over a folder of images and get back:

- a local library updated end-to-end
- one card per image
- one search record per image
- stable retrieval behavior
- a visible HTML browsing layer
- review notes instead of hidden ambiguity
