# Source Of Truth For Reference Library Indexer

## Hard execution rule

This pipeline is local-only.

- image reading is performed locally by Codex inside the skill workflow
- do not assume any external vision API
- do not assume any remote model service
- do not assume any hidden fallback to a separate image-reading tool
- switching to a cheaper model only changes the local execution mode, not the architectural responsibility of the skill

## Active-truth override

Some older architecture drafts still mention layers that are no longer active.
If any file below still mentions `product_position`, separate `shot scale`, active `finish`, or older `Close-up / Detail-only` handling as current truth, those mentions are superseded by:

1. `frozen-decisions-register-v1.md`
2. `visual-normalization-guide-v1.md`
3. `normalization-answer-capture-v1.md`
4. `recognition-indexing-instruction-clean-v1.md`
5. `resolution-log-v1.md`

Read in this order.

## 1. Build brief

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/implementer-handoff-letter-v1.md`

Purpose:
- what the executor must build
- what counts as acceptance

## 2. Package reading order

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/implementer-package-index-v1.md`

Purpose:
- map of the full architecture package
- role of each file

## 3. Frozen decisions register

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/frozen-decisions-register-v1.md`

Purpose:
- short memory layer of what is frozen, parked, or still open

## 4. Visual normalization guide

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/visual-normalization-guide-v1.md`

Purpose:
- human-readable operational normalization guide
- what counts as what
- what does not count

## 5. Working answer capture

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-answer-capture-v1.md`

Purpose:
- latest user-made clarifications before everything is rewritten downstream

## 6. Semantic authority

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/semantic-authority-compiled-v1.md`

Purpose:
- current compiled semantic truth

## 7. Upstream observation contract

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/raw-observation-contract-draft-v1.md`

Purpose:
- what must be observed before normalization

## 8. Normalized card contract

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/semantic-field-contract-frozen-draft-v1.md`

Purpose:
- fields
- cardinality
- empty states
- provenance

## 9. Recognition/indexing instruction

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/recognition-indexing-instruction-clean-v1.md`

Purpose:
- how to build the card from the image

## 10. Registries

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-registry-draft-v1.md`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-registry-frozen-subset-v1.md`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-registry-frozen-subset-v2.md`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-registry-frozen-subset-v3.md`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/normalization-registry-frozen-subset-v4.md`

Purpose:
- allowed values
- vocabulary discipline

## 11. Projection to search record

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/search-record-projection-clean-draft-v1.md`

Purpose:
- how retrieval-facing data is derived from normalized card

## 12. Retrieval logic

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/retrieval-spec-clean-draft-v1.md`

Purpose:
- normalization
- anchors
- scoring
- fallback

## 13. Tuning guardrails

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/tuning-guardrails-v1.md`

Purpose:
- what is tuning vs structural truth

## 14. Decision context

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/resolution-log-v1.md`

Purpose:
- user-made semantic decisions

## 15. Testing surfaces

- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/golden-mini-set-machine-fixtures-v1.json`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/tool-live-test-set-v2.json`
- `/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/2026-05-07_reference-index-architecture/reindexed-live-batch-v1/outputs`

Purpose:
- machine regression
- human-visible smoke test
- fresh batch reindex outputs under the current skill contract

## Never treat these as semantic truth

- old batch viewer heuristics
- old legacy batch adapters
- random labels from early prototypes
- `minimal_luxury`
- `glossy_commercial`
- outdated `shot scale` branches after the user removed this layer from the active model
- outdated `product_position` branches after the user removed this layer from the active model
- older `Close-up / Detail-only` logic after the user removed the separate scale layer from the active model
- parked helper packaging fields
- parked `finish`
