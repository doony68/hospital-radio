# Hospital Radio Song Search – setup

`index.html` is a web page that reads your "Playlists" Google Sheet live and lets you:

- search for a song or a show / film
- see how many times each song has been played and on which broadcast dates (the tab names)
- filter by date range (or use the quick buttons: last 3 months, last 12 months, this year)
- click any date to see every song played on that broadcast, in running order (click the pill to go back)
- find songs you haven't played lately with **Not played in the last…** (1, 3, 6 or 12 months)
- see a **Most played** chart of the top 10 (click a bar to search for it); it follows your filters
- switch to a "Shows & films" view to see how often each one has been played, with the dates of every song (click a date for that show's running order)
- press **Suggest Songs** for a ready-made 13-song playlist you can save as CSV (see below)

## Suggest Songs

Press **Suggest Songs** and a window opens with a 13-song playlist for your next show:

- **Last 4 shows excluded:** the 4 newest date-named tabs. Nothing played in them can appear.
- **Upbeat / slow:** each song is tagged from keywords in its title and show name. Songs with no keyword match are given whichever vibe the running order needs, and marked "(vibe is a guess)".
- **Running order:** 7 upbeat and 6 slow, alternating, so no two neighbours share a vibe. It opens with a well-known upbeat song and closes with an upbeat crowd favourite. "Well-known" and "favourite" mean songs you've played most often.
- **Variety:** it avoids repeating a show or an era, and prefers songs that haven't been played for a while.
- **Suggest again** gives a different playlist. **Save as CSV** downloads it with Position, Title, Show, Vibe and Notes columns, a summary line and the list of excluded songs. Open the file in Google Sheets with File → Import.
- If fewer than 13 songs are free to use, the window says so and offers to exclude one show fewer.

The picking is done by a formula on the page, so it's a good starting point rather than a hand-made running order.

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
