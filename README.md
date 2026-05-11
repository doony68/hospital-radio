# Radio Playlist Suggester
name: radio-playlist-suggester

# Hospital Radio Playlist Suggester

The user is a hospital radio presenter. Their Google Sheet tracks every song played
across all past shows. This skill analyses the sheet and suggests the next show's
playlist.

## Prerequisites

- Google Drive must be connected in Claude
- The user provides a Google Sheet URL or file ID

## Workflow

### Step 1 — Get the Sheet ID

Ask the user for their Google Sheet URL or file ID if not already provided.
Extract the file ID from a URL using the pattern: `/d/FILE_ID/`

---

### Step 2 — Read the Sheet

Use the **Google Drive `read_file_content` tool** with the file ID.

The sheet returns a large markdown table. Each tab (show) appears as a separate
table block separated by `\n\n| Song | Show |`.

Parse with Python:

```python
import re

table_blocks = re.split(r'\n\n\| Song \| Show \|', content)

shows = []
for i, block in enumerate(table_blocks):
    if i > 0:
        block = '| Song | Show |' + block
    rows = block.split('\n')
    songs = []
    for row in rows:
        row = row.strip()
        if not row.startswith('|') or ':-:' in row:
            continue
        parts = [p.strip() for p in row.split('|')]
        parts = [p for p in parts if p]
        if len(parts) >= 2:
            title, show = parts[0], parts[1]
            if title.lower() == 'song' or show.lower() == 'show':
                continue
            if title and show and len(title) > 1:
                songs.append({'title': title, 'show': show})
    if songs:
        shows.append({'tab': f'Show {i+1}', 'songs': songs})
```

---

### Step 3 — Exclude Last 4 Shows

```python
last4 = shows[-4:]
excluded = set()
for s in last4:
    for song in s['songs']:
        excluded.add(f"{song['title'].lower().strip()}|||{song['show'].lower().strip()}")
```

---

### Step 4 — Build Available Pool & Assign Vibe

Collect all unique songs NOT in the excluded set. Assign upbeat/slow vibe
using keyword matching on title + show name:

```python
UPBEAT_KEYWORDS = [
    'happy', 'dance', 'dancing', 'celebration', 'joy', 'fun', 'rock', 'swing',
    'jazz', 'boogie', 'rhythm', 'bounce', 'energy', 'anthem', 'fame', 'glory',
    'freedom', 'winner', 'champion', 'grease', 'mamma mia', 'chicago', 'cabaret',
    'hairspray', 'wicked', 'hamilton', 'footloose', 'flashdance', 'saturday night fever',
    'good morning', 'america', 'popular', 'defying', 'circus', 'razzle', 'stayin',
    'one night in bangkok', 'get me to the church', 'luck be a lady', 'mustang',
    'hungry eyes', 'time of my life', 'loco', 'cups', 'axel f', 'no time to die',
    'writing', 'golden eye', 'a view to a kill', 'singin', 'calamity', 'deadwood',
    'well did you', 'flash bang', 'just leave everything', 'anything you can do'
]

SLOW_KEYWORDS = [
    'memory', 'alone', 'love', 'heart', 'dream', 'angel', 'beautiful', 'forever',
    'miss', 'longing', 'tears', 'sad', 'gentle', 'quiet', 'still', 'wonder',
    'farewell', 'goodbye', 'somewhere', 'on my own', 'i dreamed', 'phantom',
    'all i ask', 'bring him home', 'who am i', 'over the rainbow', 'send in the clowns',
    'hopelessly', 'devoted', 'wind beneath', 'my heart will go on', 'i will always love',
    'beauty and the beast', 'true love', 'stars', 'lara', 'tell me on a sunday',
    'take that look', 'hello again', 'when you believe', 'electricity', 'sunrise',
    'circle of life', 'love changes everything', 'i know him so well',
    'nobody does it better', 'do you hear the people', 'this is me', 'shallow',
    'skyfall', 'chariots', 'dont cry', 'rainbow high', 'moon river', 'mrs robinson',
    'bright eyes', 'theme from', 'james bond', 'raiders', 'pink panther',
    'entertainer', 'dambusters', 'big country', 'superman', 'chitty', 'truly scrumptious'
]

import random

def guess_vibe(title, show):
    text = (title + ' ' + show).lower()
    up = sum(1 for k in UPBEAT_KEYWORDS if k in text)
    sl = sum(1 for k in SLOW_KEYWORDS if k in text)
    if up > sl:
        return 'upbeat'
    elif sl > up:
        return 'slow'
    return random.choice(['upbeat', 'slow'])

seen = set()
pool = []
for s in shows:
    for song in s['songs']:
        key = f"{song['title'].lower().strip()}|||{song['show'].lower().strip()}"
        if key not in excluded and key not in seen:
            seen.add(key)
            pool.append({
                'title': song['title'],
                'show': song['show'],
                'vibe': guess_vibe(song['title'], song['show'])
            })
```

---

### Step 5 — Select 13 Songs

**Do not call the API.** Select the 13 songs yourself using your own judgment
based on the pool, following these rules:

- Exactly 13 songs
- Target: ~7 upbeat, ~6 slow (adjust slightly for flow)
- Order for variety — no two consecutive songs with the same vibe where avoidable
- Open with a strong, recognisable upbeat track
- Close on an upbeat crowd favourite
- Aim for variety of eras and shows — avoid clustering the same musical

Assign a brief reason (1 sentence) to each pick explaining why it fits the show.

---

### Step 6 — Create Output Google Sheet

Use **Google Drive `create_file`** to create a new CSV-converted Google Sheet:

```python
today = "DD-MM-YYYY"  # use actual date
title = f"Hospital Radio Playlist Suggestion - {today}"

rows = ["Position,Title,Show,Vibe,Notes"]
for t in playlist:
    rows.append(f"{t['position']},\"{t['title']}\",\"{t['show']}\",{t['vibe']},\"{t['reason']}\"")

# Add summary and exclusion info below the playlist
rows.append("")
rows.append(f"Summary: {summary}")
rows.append("")
rows.append("Excluded (last 4 shows):")
for s in last4:
    rows.append(f"{s['tab']}: {', '.join(x['title'] for x in s['songs'])}")

csv_content = '\n'.join(rows)
```

Call `create_file` with:
- `title`: the playlist title
- `textContent`: the CSV string
- `contentMimeType`: `"text/csv"` (Google Drive will auto-convert to Sheets)

---

### Step 7 — Present Results in Chat

Show the playlist in a clear table, then share the Google Sheet link.

**Format:**

```
📊 [Sheet Title](sheet_url)

26 shows analysed · 50 songs excluded · 152 in pool

| # | Vibe | Title | Show |
|---|------|-------|------|
| 1 | 🎵 Upbeat | Song name | Show name |
...

**7 upbeat · 6 slow** — [one-line summary of the playlist feel]
```

Emoji guide: 🎵 = upbeat, 🎶 = slow

---

## Rules

- **Exactly 13 songs** — never more, never fewer
- **No songs from the last 4 shows** — enforce strictly
- **No duplicate songs** in the suggested playlist
- **No API calls for playlist selection** — Claude selects using its own judgment
- **Google Drive must be connected** — if not, tell the user to connect it
- **No source citations** in output
- Keep reasons brief (one sentence max)

## Tab Naming Note

If the sheet tabs are named with dates (e.g. "12/01/2025"), parsing by date is
more reliable. If tabs are unnamed or numeric, use order (last 4 = most recent 4).
Mention to the user that naming tabs with dates improves accuracy.

## Edge Cases

- **Pool too small** (< 13 after exclusions): Warn the user and suggest relaxing
  the exclusion window to 3 shows, then proceed
- **All same vibe**: Do your best with what's available; note the imbalance
- **Sheet unreadable**: Check Google Drive is connected; ask user to reshare

  
# Radio
Take a list of songs and gets detailed information about the related show/movie and populates a Google sheet
