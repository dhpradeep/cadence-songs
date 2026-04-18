# cadence-songs

Remote song catalog for **Cadence** — the iOS sequence-memory game.

The app fetches `songs.json` from this repo on launch, caches it locally, and falls back to bundled defaults when offline.

## Adding a song

1. Edit `songs.json`
2. Add an entry to the `songs` array:

```json
{
  "id": "kebab-case-id",
  "name": "Display Name",
  "composer": "Composer Name",
  "order": 70,
  "difficulty": 2,
  "premium": false,
  "tempo": 120,
  "keySignature": "C",
  "notes": [
    { "pitch": "C4", "duration": 0.5 },
    { "pitch": "E4", "duration": 0.5 }
  ]
}
```

3. Commit + push. The app picks up the change on next launch (no App Store update required).

## Field reference

| Field | Type | Notes |
|---|---|---|
| `id` | string | Stable, kebab-case. Used for dedupe and progress tracking. Never change after ship. |
| `name` | string | Display name |
| `composer` | string | Attribution shown under name |
| `order` | int | Lower = higher in list. Leave gaps (10, 20, 30…) so you can insert later. |
| `difficulty` | int 1–5 | Star rating shown in picker |
| `premium` | bool | `true` requires Cadence Pro. UI only — StoreKit is the real gate. |
| `tempo` | int | BPM, informational for now |
| `keySignature` | string | e.g. `C`, `Am`, `D`. Informational. |
| `notes` | array | Ordered list of `{pitch, duration}` |

### Pitch notation

Scientific pitch notation: `C4` (middle C), `D#4`, `Eb5`, etc.

Supported range in the app: **C3 through C6** (three full octaves). Notes outside this range will fall back to the nearest octave.

Accidentals: both sharps (`C#4`) and flats (`Db4`) are accepted.

### Duration

Seconds. Keep values between `0.15` and `1.5` for a playable tempo.

## Schema versioning

`version` is the schema version. Bump when adding required fields. Older apps ignore unknown fields and keep working.

`minAppVersion` tells older app builds to ignore songs that need newer capabilities.

## Licensing

Only public-domain compositions or original works please. The app ships to the App Store; copyrighted melodies will get the app rejected.

## Workflow

- Raw URL the app fetches: `https://raw.githubusercontent.com/dhpradeep/cadence-songs/main/songs.json`
- Changes go live when pushed to `main`. GitHub's CDN typically updates within minutes.
- The app validates JSON on fetch; if malformed, it keeps the last good catalog.
