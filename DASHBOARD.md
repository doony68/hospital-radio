# Hospital Radio Song Search – setup

`index.html` is a web page that reads your "Playlists" Google Sheet live and lets you:

- search for a song or a show / film
- see how many times each song has been played and on which broadcast dates (the tab names)
- filter by date range (or use the quick buttons: last 3 months, last 12 months, this year)
- click any date to see every song played on that broadcast, in running order (click the pill to go back)
- find songs you haven't played lately with **Not played in the last…** (1, 3, 6 or 12 months)
- see a **Most played** chart of the top 10 (click a bar to search for it); it follows your filters
- switch to a "Shows & films" view to see how often each one has been played
- press **Suggest Songs** to plan your next show (see below)

## Suggest Songs

The page can't create Google Sheets or choose songs by itself, so the button hands the job to Claude:

1. Press **Suggest Songs**. The page works out the last 4 shows (the 4 newest date-named tabs) and every
   song that was *not* played in them, and tags each as upbeat, slow or unsure from keywords in the title and show name.
2. Press **Copy request**, open Claude (with Google Drive connected) and paste it in.
3. Claude picks exactly 13 songs by its own judgment (about 7 upbeat and 6 slow, alternating, opening and closing
   on upbeat favourites, mixing eras and shows) and saves them as a new Google Sheet in your Drive.

The request includes all your rules, so there's nothing else to type. If fewer than 13 songs are free to use, the page warns you.

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
