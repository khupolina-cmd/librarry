# Default Local Library Layout

Use this only when the project does not already define a stronger library structure.

```text
reference-library/
├── images/
│   ├── originals/
│   └── previews/
├── manifests/
│   └── batch-manifest.csv
├── raw-observation/
│   └── <asset-id>.raw.json
├── normalized-cards/
│   └── <asset-id>.card.json
├── search-records/
│   └── <asset-id>.search.json
├── review/
│   └── review.md
├── logs/
│   └── ingest-log.md
└── ui/
    ├── index.html
    ├── data/
    └── assets/
```

## Notes

- Keep originals stable.
- Do not rename originals unless the user explicitly asks.
- `asset-id` should come from a stable manifest, not from casual UI order.
- The UI layer should read structured data; it should not be the first place where semantics are invented.

