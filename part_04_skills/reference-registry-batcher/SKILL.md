---
name: reference-registry-batcher
description: Build and update a structured reference-library registry from a batch of images. Use this skill whenever the user gives a folder or batch of refs/anti-refs and wants Claude to auto-fill technical tags and rich human scene descriptions, preserve a stable naming vocabulary, update registry history, and keep the batch compatible with future web-search bridging. Also use it when the user wants only layer 1 left for manual completion while Claude fills layers 2 and 3.
---

# Reference Registry Batcher

Use this skill to process image batches for a reference library that has:

- `layer 1` = the user's own reason for saving or rejecting the image
- `layer 2` = structured technical fields
- `layer 3` = detailed human scene description

The point of this skill is not to "caption images."
The point is to keep one stable registry system that works for:

- local library search
- anti-reference clarification
- future web-search bridging

## Core rule

Do not invent a fresh schema every batch.

Always anchor the new batch to the existing:

- registry
- vocabulary
- history of decisions
- local/web bridge rules

If the project already has those files, reuse them.
Do not silently fork the language.

## What this skill should load first

Before processing a batch, look for these project files if they exist:

- `_analysis/схема-каталога-v1.md`
- `_analysis/registry-history-v1.md`
- `_analysis/local-web-bridge-v1.md`
- `_analysis/light-taxonomy-v1.md`
- current registry CSV in `catalog/`

If a file is missing, continue, but note the missing dependency in the output.

## Expected inputs

Typical inputs:

- path to batch folder
- whether the batch is `ref`, `anti_ref`, or mixed
- current registry CSV
- vocabulary / history files
- optional anti-ref folder for comparison

If the user gives only a folder, infer the rest from project context before asking questions.

## Output contract

For each batch, produce or update:

1. `updated batch csv`
2. `review list` for the user
3. `vocabulary delta`
4. `history delta`
5. `web-bridge readiness notes`

The user should not be asked to fill technical fields first.
Claude should fill `layer 2` and `layer 3` first and hand back only the user-facing layer plus review points.

## Registry structure

Assume each image record should support at least:

- stable ID
- path
- set type
- source layer
- layer 1 user reason
- layer 2 structured fields
- layer 3 scene description
- web-bridge placeholder fields

When a project-specific registry already exists, preserve its columns unless the user explicitly asks to refactor them.

## Layer logic

### Layer 1

This belongs to the user.

For `ref` images:

- leave the "why I saved this" field empty unless the user explicitly asked Claude to draft it

For `anti_ref` images:

- leave the user-reason field empty unless the user explicitly asked Claude to draft it

Do not pretend to know the user's inner reason better than they do.

### Layer 2

Claude fills structured technical fields from the approved vocabulary.

Typical examples:

- product type
- setup
- product count
- container type
- label presence
- light direction
- light quality
- light contrast
- light pattern
- palette family
- composition
- props / materials
- effects
- mood
- source fields
- embedding placeholder

### Layer 3

Claude writes a rich human description in Russian.

This is not a one-line caption.
It should describe:

- what is visually happening
- what the scene is doing
- what kind of object-language the frame carries

The description should be useful both for:

- semantic retrieval
- future query parsing

## Two batch modes

### Mode A: refs

For normal references:

- fill layer 2
- fill layer 3
- leave layer 1 for the user
- note only truly ambiguous fields for review

### Mode B: anti_refs

For anti-references:

- fill layer 2
- fill layer 3
- add anti-side technical interpretation where useful
- identify what visually makes the image a bad fit in structured terms when the schema allows it
- still leave the user's own subjective rejection reason as a user-owned field unless asked otherwise

Anti-refs are not optional side material.
Treat them as part of the full picture of the system.

## Vocabulary discipline

Never let the vocabulary drift casually.

During a batch:

- prefer existing approved terms
- if a record does not fit, mark the conflict
- do not casually spawn synonyms

At the end of the batch:

- create a `vocabulary delta`
- list proposed new terms
- say why the current vocabulary was insufficient

Only then should the project vocabulary expand.

## History discipline

The history file is not decorative.

After each batch, write a short history delta:

- what batch was processed
- what important decisions were reused
- what new tensions were found
- what vocabulary changed
- what remains unresolved

Do not bury this only in chat.
Persist it to a project file when appropriate.

## Web-bridge discipline

This skill must keep the registry compatible with future web search.

That means each record should remain compatible with a future flow like:

`query -> query spec -> local retrieval -> web candidate retrieval -> normalization -> reranking -> filtering`

So while processing local batches, make sure records remain usable for future external candidates too.

That means preserving or creating these kinds of bridge fields when the project schema supports them:

- `set_type`
- `source_type`
- `source_url`
- `scene_description_ru`
- `layer_1_user_reason_ru`
- structured vocabulary fields
- `embedding_id`

Do not reduce the batch to a local-only table.

## Processing workflow

When invoked, work in this order:

1. detect the batch and its mode
2. load current registry, vocabulary, history, and bridge docs
3. inspect the images
4. fill `layer 2`
5. fill `layer 3`
6. flag uncertain rows
7. produce a compact user review handoff for `layer 1`
8. write vocabulary delta
9. write history delta
10. confirm web-bridge readiness

## Review handoff format

Do not dump raw chaos on the user.

Hand off review like this:

- link to updated CSV
- link to batch overview
- short list of rows that need user attention
- simple instruction for filling layer 1

If possible, group review needs into:

- `obvious`
- `uncertain`
- `new term candidate`

## What to avoid

- do not ask the user to manually fill technical fields first
- do not rename stable vocabulary on a whim
- do not force one "main reason" onto a ref
- do not treat anti-refs as secondary
- do not make a local-only registry that later breaks web-search plans
- do not keep important logic only in conversation without updating project files

## Example use cases

**Example 1**
User: `вот пачка из 40 фото, добей layer 2 и 3, а мне оставь why saved`

Do:

- process batch
- update CSV
- keep layer 1 empty
- output review handoff

**Example 2**
User: `добавила анти-рефы, разложи их так же как рефы`

Do:

- process anti-ref batch with the same registry logic
- fill technical layer and human descriptions
- record anti-side signals when schema supports them

**Example 3**
User: `после этого батча обнови словарь и историю`

Do:

- write concise deltas
- persist them to project files

## Success condition

This skill is working if, after a new image batch:

- the registry is fuller
- the vocabulary is more stable
- the history is more explicit
- the user only has to fill their own reason layer and check edge cases
- the batch remains compatible with future web search
