# Hospital Radio /play Skill
The user is a hospital radio presenter playing music from movies and musicals to an
audience of hospital patients, nurses, and doctors. The show is pre-recorded.
Trigger
This skill triggers when the user types /play or any equivalent request for song
research for their hospital radio show.
Workflow
Step 1 — Ask for the track list
Respond with exactly:

"Ready! Please give me your list of songs and the shows/films they're from — I'll
research each one and build your reference spreadsheet."

Wait for the user's list before proceeding.
Step 2 — Research each track
For every song the user provides, use web_search to find:
FieldWhat to findSongExact song titleShowFilm or stage musical nameYear ReleasedBoth film year AND original stage year (e.g. "Stage: 1978 / Film: 1996")Lead Film StarsMain cast of the film versionLead Stage StarsOriginal Broadway/West End cast; note any major revivalsComposer/LyricistFull credits — music AND lyrics separately if different peopleGenreFilm genre + musical style (e.g. "Biographical Drama / Soul")Synopsis3–4 sentence story summary a radio presenter can read aloudInteresting Facts4–6 compelling, broadcaster-friendly facts (anecdotes, records, surprises) — formatted as bullet pointsUK Chart InfoUK Singles Chart peak position and year, if the song was released as a single
Research tips:

Search each song individually: "[Song Title]" "[Show Name]" history
For UK chart positions, search "[Song Title]" UK chart official charts
Prioritise facts that are surprising, emotional, or will delight a mixed hospital audience
For instrumental tracks (e.g. Chariots of Fire), note the film's Awards and cultural impact
If a song has multiple well-known versions (e.g. a concept album version vs stage vs film),
note the most famous version and the original

Step 3 — Build the spreadsheet
Use openpyxl to create a formatted .xlsx file saved to /mnt/user-data/outputs/.
Exact column order

Song
Show
Year Released (Movie/Stage)
Lead Film Stars
Lead Stage Stars
Composer/Lyricist
Genre
Synopsis
Interesting Facts
UK Chart Info

Formatting requirements

Header row: Dark blue fill (1F4E79), white bold Arial 11pt, centred, height 35
Data rows: Alternating white / light blue (D6E4F0), Arial 10pt, top-aligned, wrap text
Row height: 120 for data rows
Interesting Facts cell: Format each fact as a bullet point using •  prefix, separated by \n (e.g. "• Fact one\n• Fact two\n• Fact three"). Wrap text is already enabled so line breaks will display correctly.
Column widths (approximate): Song 25, Show 30, Year 28, Film Stars 35, Stage Stars 35,
Composer 35, Genre 20, Synopsis 55, Facts 60, UK Chart 35
Freeze row 1
File name: Hospital_Radio_Playlist_[date].xlsx or similar

Python skeleton
pythonfrom openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from openpyxl.utils import get_column_letter

wb = Workbook()
ws = wb.active
ws.title = "Hospital Radio Playlist"

headers = [
    "Song", "Show", "Year Released (Movie/Stage)", "Lead Film Stars",
    "Lead Stage Stars", "Composer/Lyricist", "Genre", "Synopsis",
    "Interesting Facts", "UK Chart Info"
]

header_fill = PatternFill("solid", start_color="1F4E79", end_color="1F4E79")
header_font = Font(name="Arial", bold=True, color="FFFFFF", size=11)
header_align = Alignment(horizontal="center", vertical="center", wrap_text=True)

for col, header in enumerate(headers, 1):
    cell = ws.cell(row=1, column=col, value=header)
    cell.fill = header_fill
    cell.font = header_font
    cell.alignment = header_align

ws.row_dimensions[1].height = 35
ws.freeze_panes = "A2"

### When populating Interesting Facts, format as bullet points:
facts = ["Fact one", "Fact two", "Fact three"]
cell.value = "\n".join(f"• {fact}" for fact in facts)

### ... populate rows, set column widths, save ...

wb.save("/mnt/user-data/outputs/Hospital_Radio_Playlist.xlsx")
Step 4 — Present the file
Use present_files to share the spreadsheet, then add a brief conversational note
highlighting 2–3 of the most interesting facts from the batch — things that would
make great on-air chat.
Quality bar for "Interesting Facts"
Good facts for a hospital radio audience:

Behind-the-scenes drama (casting changes, songs written overnight)
Record-breaking chart or box office achievements
Surprising connections (e.g. the songwriter also wrote X)
Human interest (a star's personal connection to the song)
Things that will surprise both older and younger patients

Avoid dry catalogue entries. Every fact should be something the presenter could
say on air and get a reaction.
Output format rules

No sources/citations in the spreadsheet
Synopsis should be readable aloud in under 30 seconds
Use plain English — no jargon
UK chart info format: UK #[position] ([year]) — e.g. UK #1 for 4 weeks (Feb 1985)
(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.nonce='E/Wsx4lYTHo12IOv64WioA==';d.innerHTML="window.__CF$cv$params={r:'9f8995010850f400',t:'MTc3ODI1NTAyNw=='};var a=document.createElement('script');a.nonce='E/Wsx4lYTHo12IOv64WioA==';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();
