# Singles Catalogue - Search

A search page for my collection of singles. Open `index.html` (or visit `/singles/` on the Pages site).

- Search by artist, track, genre, year or notes. Every word must match, in any order.
- Filter by genre or decade, sort, or show only singles with no notes yet.
- Click an artist, year or genre on a card to filter by it.
- Press `/` to jump to the search box.

## Updating the catalogue

The data lives in `data.js` as one object per single:

```js
{ "cat": 1, "artist": "The Art Of Noise", "title": "Close (To The Edit)",
  "notes": "...", "genre": "Synth-pop / Electronic", "year": 1984 }
```

- Separate several genres with ` / `. Each one appears in the Genre drop-down.
- `aka` (optional) holds a corrected spelling so searches still find the record, e.g. "Freeez".
- Leave `notes` as `""` to flag a single as needing research.

The data is a snapshot of the "Singles Catalogue" Google Sheet. To refresh it,
ask Claude to re-read the sheet and regenerate `data.js`.
