# Font Record Shape

Canonical field reference for a single entry in `font-library/fonts.json`.

## Required fields

```json
{
  "id": "string — slugified family name, lowercase hyphen-separated, unique across the library",
  "family": "string — display name, exactly as the font should appear in UI",
  "google_fonts_id": "string | null — Google Fonts slug for CSS2 API, e.g. 'Playfair+Display'; null if local-only",
  "google_fonts_variants": "string | null — variant query string, e.g. 'ital,wght@0,400..900;1,400..900'; null if not on Google Fonts",
  "local_path": "string — relative path from font-library/ to the font's subfolder, e.g. 'fonts/playfair-display/'",
  "files": [
    {
      "file": "string — path relative to font-library/, e.g. 'fonts/playfair-display/PlayfairDisplay-Regular.woff2'",
      "weight": "number — CSS font-weight integer, e.g. 400",
      "italic": "boolean — true if this is an italic style"
    }
  ],
  "default_weight": "number — recommended starting weight for specimens and UI rendering",
  "weight_range": ["number — min weight", "number — max weight"],

  "tags": {
    "style":    ["array of style vocabulary terms"],
    "mood":     ["array of mood/character terms in Russian"],
    "function": ["array of typographic function terms"],
    "context":  ["array of project/brand context terms"]
  },

  "technical": {
    "has_cyrillic":    "boolean",
    "is_variable":     "boolean",
    "styles_count":    "number — total count of distinct styles (weight × italic combinations available)",
    "license_status":  "web_use_allowed | checked_private_local | unknown",
    "license_note":    "string — short description of the specific license, e.g. 'SIL Open Font License 1.1'"
  },

  "description": "string — 1–2 sentence character summary in Russian, grounded in observable typographic properties",

  "specimen": {
    "hero":   "string — short ALL-CAPS or display-register line for the font card hero",
    "poster": "string — evocative multi-word phrase for a typographic poster",
    "info":   "string — neutral technical or product-register line testing small-size legibility"
  },

  "index": {
    "style_label":    "string — combined style descriptor in English, usable as a search query fragment",
    "mood_label":     "string — combined mood/character label in Russian, comma-separated",
    "function_label": "string — primary use cases in Russian, comma-separated",
    "context_label":  "string — brand/project types in English, comma-separated",
    "strength":       "string — one sentence in Russian: what makes this font the right choice over others in the library"
  }
}
```

## Notes

### `files` array

- Empty array `[]` is valid when using Google Fonts CDN only (no local files placed yet).
- When local woff2 files exist, list every file here.
- The HTML viewer gives priority to local files over CDN.

### `google_fonts_variants` format

This is the `family` parameter value for the Google Fonts CSS2 API.

Examples:
- `"ital@0;1"` — regular + italic, single weight
- `"ital,wght@0,400..900;1,400..900"` — variable weight range, both styles
- `"ital,wght@0,300;0,400;0,700;1,400"` — specific named instances

### `default_weight`

Pick the weight that best represents the font for initial rendering in the library viewer.
Usually `400` (Regular) or `300` (Light) for display serifs.

### `id` rules

- All lowercase
- Hyphens between words, no underscores
- Must match the subfolder name under `fonts/`
- Must be unique; check existing `fonts.json` before writing

### `not_collected`

Use the string `"not_collected"` for any field that cannot be honestly filled.
Never fabricate certainty — it is better to leave a visible gap than a confident wrong value.
