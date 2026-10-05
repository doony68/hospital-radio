# Hospital Radio Song Search – setup

`index.html` is a web page that reads your "Playlists" Google Sheet live and lets you:

- search for a song or a show / film
- see how many times each song has been played and on which broadcast dates (the tab names)
- filter by date range (or use the quick buttons: last 3 months, last 12 months, this year)
- switch to a "Shows & films" view to see how often each one has been played

New tabs you add to the sheet appear automatically (tabs named like `06-10-2026`, day-month-year).

## One-off setup (about 10 minutes)

### 1. Share the sheet so the page can read it
In Google Sheets: **Share → General access → Anyone with the link → Viewer**.
(People can only see it if they have the link. They can't edit it.)

### 2. Get a free Google API key
1. Go to <https://console.cloud.google.com/> and create a project (any name).
2. **APIs & Services → Library** → search **Google Sheets API** → **Enable**.
3. **APIs & Services → Credentials → Create credentials → API key**.
4. Click the new key → **Edit** and set two restrictions:
   - **API restrictions** → Restrict key → tick **Google Sheets API**.
   - **Website restrictions** → add your page address, e.g. `https://doony68.github.io/*`
5. Copy the key into the `API_KEY` line of `config.js` and commit.

The key can only read sheets that are shared publicly, and the restrictions stop other
websites using it, so it is fine for it to sit in the public repo.

### 3. Switch on GitHub Pages
Repo **Settings → Pages → Build and deployment**: Source = **Deploy from a branch**,
Branch = `main`, folder = `/ (root)` → **Save**.
After a minute your page is at `https://doony68.github.io/hospital-radio/`.

## Notes
- Columns are found by their header names (Song, Show, Year Released, Lead Film Stars, …),
  so extra columns such as "UK Chart Info" are picked up automatically.
- The same song under the same show on different dates is counted as one song played several times.
  Spelling differences in capitals, punctuation or accents don't matter, but different spellings of
  the title itself would show as separate songs.
- Tabs that aren't dates are still read, but are left out when a date filter is on.
