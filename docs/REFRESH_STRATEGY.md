REFRESH_STRATEGY

Purpose

This document defines when data should be downloaded from MyFantasyLeague, when existing data should be reused, and how refreshes should behave across multiple leagues and seasons.

The goals are to:

- Avoid unnecessary API calls
- Keep current-season data fresh
- Preserve completed historical seasons
- Support live draft use
- Prevent duplicate data
- Allow multiple leagues to coexist safely
- Make refresh behavior predictable

---

Core Refresh Principles

1. Completed historical seasons should normally be treated as cached data.

2. Current-season data may change and should be refreshed as needed.

3. Draft-related data may need frequent manual refreshes during a live draft.

4. Static or slow-changing global data should not be repeatedly downloaded without reason.

5. A refresh should only affect the selected LeagueID and Year unless the user explicitly requests a broader update.

6. Refresh logic must never overwrite another league's data accidentally.

7. Repeated refreshes must not create duplicate records.

8. Every refreshable RAW table must have a defined key.

9. If an API call fails, existing valid data should not be erased.

10. Historical data should only be rebuilt when the user deliberately requests a rebuild or when the stored data is known to be incomplete.

---

Refresh Categories

Every data source should belong to one of the following categories.

Category A — Global / Slow-Changing

Examples:

- Player directory
- Player names
- Positions
- NFL teams
- Global player identifiers

Typical behavior:

- Refresh manually
- Refresh at the beginning of a new season
- Refresh when rookies or player-team changes matter
- Do not refresh every time the workbook opens

---

Category B — League Configuration

Examples:

- League name
- Franchise list
- Divisions
- League settings
- Scoring rules
- Host/server information

Typical behavior:

- Refresh when a league is first added
- Refresh at the beginning of a season
- Refresh manually if league settings change
- Cache completed-season configuration

---

Category C — Current Season Live Data

Examples:

- Rosters
- Transactions
- Standings
- Weekly results
- Player scores
- Lineups

Typical behavior:

- Refresh manually
- Refresh after games or transactions
- Refresh weekly during the season
- Do not repeatedly refresh completed seasons

---

Category D — Draft Live Data

Examples:

- Draft picks
- Current rosters
- Available-player state

Typical behavior:

- Refresh frequently during a draft
- Prefer a manual Refresh Draft button
- Refresh only the minimum data necessary for draft decisions

---

Category E — Historical Data

Examples:

- Past player scores
- Past weekly results
- Past drafts
- Past standings

Typical behavior:

- Pull once
- Store permanently
- Do not repeatedly call MFL for completed seasons
- Rebuild only when data is missing, corrupted, or deliberately refreshed

---

START_HERE Controls

START_HERE should eventually provide the following controls:

- Active League
- Active Year
- History Start Year
- Refresh Current League
- Refresh Draft
- Update History
- Refresh Players
- Last Refresh
- Status

Optional future controls:

- Rebuild Current Year
- Rebuild History
- Refresh All Enabled Leagues

---

Refresh Current League

Purpose

Refresh current-season data for the selected LeagueID and Year.

Reads From

START_HERE:

- Active League
- Active Year

Potential Actions

Refresh:

- LEAGUE_INFO_RAW
- FRANCHISES_RAW
- ROSTERS_RAW
- TRANSACTIONS_RAW
- WEEKLY_RESULTS_RAW
- PLAYER_SCORES_RAW
- other current-season data as modules are added

Behavior

For each table:

1. Read Active League and Active Year.
2. Call the verified MFL endpoint.
3. Validate the response.
4. If the call succeeds, replace or upsert only rows for that LeagueID and Year.
5. Preserve all other leagues and years.
6. Update LastRefresh.
7. Set status to READY.

If a call fails:

- Do not delete existing valid rows.
- Record the error.
- Set status to ERROR or PARTIAL.
- Continue only when safe.

---

Refresh Draft

Purpose

Refresh only the information required during a live draft.

Reads From

START_HERE:

- Active League
- Active Year

Minimum Draft Refresh

Likely refresh:

- DRAFT_RAW
- ROSTERS_RAW, if needed for availability

Then rebuild:

- DRAFT_POOL
- DRAFT_ANALYSIS
- DRAFT_BOARD

Avoid During Draft Refresh

Do not unnecessarily refresh:

- Historical scoring
- Completed prior seasons
- Static player history
- Old standings
- Old transactions
- Other unrelated modules

Manual Refresh Preferred

The preferred design is a button labeled:

Refresh Draft

The user controls when the sheet updates.

This avoids:

- Excessive API calls
- Unexpected display changes
- Unnecessary refreshes after every spreadsheet recalculation

---

Update History

Purpose

Build or extend historical data for the selected league.

Reads From

START_HERE:

- Active League
- Active Year
- History Start Year

Example:

LeagueID = 49286
HistoryStart = 2022
ActiveYear = 2026

The requested history range is:

2022
2023
2024
2025
2026

Process

For each year:

1. Check whether the required historical data already exists.
2. Determine whether the season is complete.
3. If complete and already stored, skip it.
4. If missing, retrieve it.
5. If current/incomplete, refresh it.
6. Store records with LeagueID and Year.

Example

Stored:

2022 COMPLETE
2023 COMPLETE
2024 COMPLETE
2025 COMPLETE

Current:

2026 IN PROGRESS

Update History should normally:

Skip 2022
Skip 2023
Skip 2024
Skip 2025
Refresh 2026

---

Completed Season Logic

A season should be treated as historical only when the project determines it is complete.

The exact method for determining completion should be documented once verified.

Potential methods may include:

- Current date relative to season
- MFL league status
- Final configured fantasy week
- Explicit project flag

Do not assume a season is complete solely because the calendar year changed.

---

Historical Season Status

A future tracking table may be useful.

Example:

DATA_STATUS

LeagueID| Year| DataType| Status| LastUpdated
49286| 2024| PLAYER_SCORES| COMPLETE| timestamp
49286| 2025| PLAYER_SCORES| COMPLETE| timestamp
49286| 2026| PLAYER_SCORES| CURRENT| timestamp
71822| 2026| DRAFT| CURRENT| timestamp

Possible Status values:

MISSING
CURRENT
COMPLETE
PARTIAL
ERROR

This table is optional for the first version but may become useful as historical data grows.

---

Refresh Players

Purpose

Update the global player directory.

Target

PLAYERS_RAW

Typical Frequency

Refresh when:

- A new season begins
- Rookie PlayerIDs are added
- NFL team assignments change
- Position information changes
- The user manually requests an update

Do not refresh PLAYERS_RAW every time Refresh Draft is pressed unless necessary.

---

ADP Refresh

ADP refresh behavior depends on the verified ADP source.

Before implementation, determine:

- Is ADP global?
- Is it league-specific?
- Is it year-specific?
- How often does it change?
- Does MFL expose it through the API?
- Does the source provide PlayerID?

Likely behavior during draft season:

- Refresh periodically
- Refresh manually before a draft
- Optionally refresh with Refresh Draft if the call is lightweight and useful

Do not hard-code this behavior until the source is verified.

---

Raw Table Update Methods

Two primary update methods should be used.

Replace Scope

Delete and replace only the records belonging to a specific scope.

Example for ROSTERS_RAW:

Delete:
LeagueID = 49286
Year = 2026

Insert:
fresh 49286 / 2026 roster records

Do not clear the entire sheet.

This method works well for snapshot-type data.

Examples:

- Rosters
- Franchises
- Standings
- Current draft results

---

Upsert

Update an existing record if its key already exists.

Insert if it does not exist.

Example key:

LeagueID + Year + Week + PlayerID

This is useful for historical or accumulating data.

Examples:

- Weekly player scoring
- Transactions
- Draft picks
- Weekly results

---

Duplicate Prevention

Every RAW table must define its record key before refresh code is written.

Examples:

PLAYERS_RAW
PlayerID

FRANCHISES_RAW
LeagueID + Year + FranchiseID

DRAFT_RAW
LeagueID + Year + PickNumber

PLAYER_SCORES_RAW
LeagueID + Year + Week + PlayerID

Repeated refreshes should produce the same logical dataset, not duplicate rows.

---

Error Handling

A failed refresh must not destroy previously valid data.

Preferred process:

1. Fetch API response.
2. Confirm response is valid.
3. Parse response.
4. Confirm required fields exist.
5. Only then modify the RAW sheet.

Avoid:

Clear sheet
↓
Call API
↓
API fails
↓
Sheet is now empty

Prefer:

Call API
↓
Validate response
↓
Success?
   YES → replace/upsert records
   NO  → preserve existing data

---

Refresh Status

START_HERE should eventually show refresh state.

Suggested values:

READY
REFRESHING
COMPLETE
PARTIAL
ERROR

Suggested fields:

Field| Example
Last Refresh| 8/27/2026 7:15 PM
Last League| 49286
Last Year| 2026
Status| READY
Message| Draft data refreshed

---

Refresh Logging

A future REFRESH_LOG sheet may record:

- Timestamp
- LeagueID
- Year
- RefreshType
- Endpoint/DataType
- RowsInserted
- RowsUpdated
- Status
- ErrorMessage

Example:

Timestamp| LeagueID| Year| Type| Data| Rows| Status
timestamp| 49286| 2026| DRAFT| draftResults| 68| SUCCESS

This is optional initially but recommended as the project grows.

---

Multi-League Refresh

The first implementation should refresh one selected league at a time.

Future option:

Refresh All Enabled Leagues

Process:

1. Read LEAGUES.
2. Select rows where Enabled = TRUE.
3. Loop through each LeagueID.
4. Refresh only required data.
5. Log results.
6. Continue safely if one league fails.

Do not make multi-league bulk refresh the first implementation.

First prove that one active league refreshes correctly.

---

Refresh All Enabled Leagues — Future Behavior

Potential use cases:

- Weekly scoring update
- Standings update
- Historical maintenance
- Roster snapshots

It should not automatically run every expensive API call for every league.

Each module should decide whether bulk refresh is appropriate.

---

Draft-Day Behavior

During a live draft, the preferred sequence is:

User presses Refresh Draft
        ↓
Read LeagueID + Year
        ↓
Fetch latest draft results
        ↓
Validate response
        ↓
Update DRAFT_RAW
        ↓
Refresh roster state if necessary
        ↓
Rebuild available-player pool
        ↓
Recalculate draft analysis
        ↓
Update DRAFT_BOARD
        ↓
Record timestamp

Historical data should remain untouched.

---

Weekly In-Season Behavior

Typical weekly workflow may become:

Refresh Current League
        ↓
Rosters
Transactions
Weekly Results
Player Scores
Standings
        ↓
Analysis tables
        ↓
Display pages

Historical completed years remain untouched.

---

Offseason Behavior

During the offseason:

Refresh occasionally:

- Player directory
- League configuration
- Rosters
- Draft setup
- ADP

Update History once the prior season is finalized.

---

New League Workflow

When adding a new league:

1. Add LeagueID to LEAGUES.
2. Set CurrentYear.
3. Set HistoryStart.
4. Pull league information.
5. Validate league.
6. Pull franchises.
7. Pull current roster data.
8. Optionally build requested history.

Existing leagues remain untouched.

---

New Season Workflow

At the beginning of a new season:

1. Update CurrentYear in LEAGUES.
2. Confirm prior season is complete.
3. Mark prior historical data complete.
4. Refresh global players.
5. Pull new-season league configuration.
6. Pull franchises.
7. Pull current rosters.
8. Begin current-season refresh cycle.

No new workbook should be required.

---

Source-of-Truth Rule

Refresh decisions should be based on:

1. Verified MFL API documentation
2. Saved sample responses
3. Stored RAW data
4. Refresh status metadata

Do not design refresh behavior based solely on assumptions about what an endpoint might return.

---

Initial Implementation

The first refresh implementation should remain intentionally small.

Phase 1

Implement:

refreshLeagueInfo()

Using:

- Active League
- Active Year

Populate:

LEAGUE_INFO_RAW

Prove:

- START_HERE controls the request
- LeagueID is not hard-coded
- Year is not hard-coded
- API authentication works
- Response validation works
- Existing data is not destroyed on failure

---

Phase 2

Implement:

refreshPlayers()

Populate:

PLAYERS_RAW

---

Phase 3

Implement:

refreshDraft()

Populate:

DRAFT_RAW

Then rebuild:

DRAFT_POOL
DRAFT_BOARD

---

Design Goal

The user should always be able to answer:

- What data am I refreshing?
- Which league am I refreshing?
- Which year am I refreshing?
- Is this current data or historical data?
- Will this overwrite anything?
- When was it last updated?
- Did the refresh succeed?

Refresh behavior should be deliberate, visible, and safe.
