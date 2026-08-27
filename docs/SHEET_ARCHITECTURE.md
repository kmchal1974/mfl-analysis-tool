
SHEET_ARCHITECTURE

Purpose

The Google Sheets workbook is the user-facing interface for the MFL analysis system.

The workbook should separate:

1. Configuration
2. Raw data
3. Analysis
4. Display

This separation keeps API pulls, calculations, and presentation from becoming tightly coupled.

The workbook must support multiple leagues and multiple seasons.

---

Architecture Overview

START_HERE
↓
LEAGUES
↓
RAW DATA
↓
ANALYSIS
↓
DISPLAY

The project should avoid using one giant sheet for both raw data and presentation.

---

Layer 1 — Configuration

START_HERE

Purpose:

Central control page for the workbook.

Suggested fields:

- Active League
- Active Year
- History Start Year
- Refresh Current League
- Refresh Draft
- Update History
- Last Refresh
- Status

Suggested layout:

Setting| Value
Active League| dropdown
Active Year| dropdown
History Start Year| 2022
Last Refresh| timestamp
Status| READY

The Active League dropdown should be driven by the LEAGUES sheet.

No League ID should be hard-coded elsewhere when START_HERE can provide it.

---

LEAGUES

Purpose:

Registry of leagues available to the tool.

Suggested columns:

- Enabled
- LeagueID
- LeagueName
- CurrentYear
- HistoryStart
- LastRefresh
- Notes

Example:

Enabled| LeagueID| LeagueName| CurrentYear| HistoryStart
TRUE| 49286| Example League| 2026| 2022

This sheet should allow roughly a dozen leagues to coexist in the same workbook.

---

Layer 2 — Raw Data

Raw sheets should contain data pulled directly from MFL or another approved source.

Raw sheets should not be formatted as user dashboards.

Raw data should remain as close as practical to the source response.

League-specific raw sheets must include LeagueID and Year.

---

PLAYERS_RAW

Purpose:

Global player directory.

Suggested columns:

- PlayerID
- Name
- Position
- NFLTeam
- PlayerType
- LastUpdated

This table should be global rather than duplicated for each league.

---

LEAGUE_INFO_RAW

Purpose:

League configuration and identifying information.

Suggested columns may include:

- LeagueID
- Year
- LeagueName
- Host
- LeagueType
- NumberOfFranchises
- Other verified MFL fields

Exact columns should be based on the verified API response.

---

FRANCHISES_RAW

Purpose:

League franchise information.

Suggested columns:

- LeagueID
- Year
- FranchiseID
- FranchiseName
- OwnerName

Exact fields should follow the API response.

---

ROSTERS_RAW

Purpose:

League roster assignments.

Suggested columns:

- LeagueID
- Year
- FranchiseID
- PlayerID
- RosterStatus

---

DRAFT_RAW

Purpose:

Draft selections.

Suggested columns:

- LeagueID
- Year
- PickNumber
- Round
- FranchiseID
- PlayerID
- Timestamp

Exact fields should follow MFL's verified draft response.

---

PLAYER_SCORES_RAW

Purpose:

Weekly player scoring history.

Suggested columns:

- LeagueID
- Year
- Week
- PlayerID
- Points

This table should support multiple seasons in the same sheet.

Do not create separate sheets such as:

- 2024_SCORES
- 2025_SCORES
- 2026_SCORES

Store the year as a field instead.

---

ADP_RAW

Purpose:

Store ADP information from the verified source.

Suggested columns may include:

- Year
- PlayerID
- ADP
- ADPRank
- PositionADP
- Source

LeagueID should only be included if the ADP source is actually league-specific.

Do not assume ADP is league-specific until verified.

---

TRANSACTIONS_RAW

Future module.

Suggested identifying fields:

- LeagueID
- Year
- TransactionID
- Timestamp
- FranchiseID
- PlayerID
- TransactionType

---

WEEKLY_RESULTS_RAW

Future module.

Suggested identifying fields:

- LeagueID
- Year
- Week
- FranchiseID
- OpponentID
- Score

---

Layer 3 — Analysis

Analysis sheets combine raw tables and calculate derived information.

These sheets may use formulas, Apps Script, or both.

They should not make API calls directly unless there is a specific design reason.

---

DRAFT_POOL

Purpose:

Create the current draftable player pool.

Potential inputs:

- PLAYERS_RAW
- ROSTERS_RAW
- DRAFT_RAW
- ADP_RAW
- PLAYER_SCORES_RAW

Potential columns:

- PlayerID
- Name
- Position
- NFLTeam
- Available
- ADP
- ADPRank
- PreviousYearPoints
- PreviousYearPPG
- PreviousYearPosRank

Availability should be calculated rather than inferred from the player name.

---

DRAFT_ANALYSIS

Purpose:

Calculate draft decision-support metrics.

Potential calculations:

- ADP Rank
- Previous-Year Rank
- Previous-Year PPG Rank
- ADP versus Previous-Year Rank
- Positional Rank
- Historical Average
- Value Score
- Trend Score

This sheet should contain calculations that the final draft board can sort or filter.

---

PLAYER_HISTORY

Purpose:

Build multi-year player histories.

Potential columns:

- LeagueID
- PlayerID
- Name
- Year
- Games
- TotalPoints
- PPG
- PositionRank
- Trend

Future analysis may include:

- Three-year average
- Weighted average
- Best season
- Worst season
- Consistency
- Year-over-year change

---

LEAGUE_ANALYSIS

Future module.

Purpose:

League-wide analysis.

Potential uses:

- Power rankings
- Franchise strength
- Positional concentration
- Scoring trends
- Historical league comparisons

---

Layer 4 — Display

Display sheets are the pages intended for actual use.

They should be clean and easy to read.

They should not contain raw API responses.

---

DRAFT_BOARD

Purpose:

Live draft decision page.

Possible controls:

- Position filter
- Sort method
- Available only
- Active league
- Active year

Possible columns:

- Rank
- Player
- Position
- NFL Team
- ADP
- Previous-Year Points
- Previous-Year PPG
- Positional Rank
- Value Indicator

The draft board should update after Refresh Draft is triggered.

---

PLAYER_LOOKUP

Purpose:

Inspect one player's history.

Potential display:

- Player name
- Position
- NFL team
- Multi-year scoring
- PPG
- Positional rank
- Trend
- League-specific scoring comparisons

---

LEAGUE_DASHBOARD

Future module.

Purpose:

Display league-level information for the selected LeagueID and Year.

Potential sections:

- Standings
- Franchise summaries
- Top scorers
- Transactions
- Trends
- Power rankings

---

Refresh Buttons

The workbook should eventually support three primary actions.

Refresh Current League

Reads Active League and Active Year from START_HERE.

Refreshes current league-specific data.

---

Refresh Draft

Updates only data needed during an active draft.

Likely includes:

- DRAFT_RAW
- ROSTERS_RAW
- Availability calculations

Historical scoring should not be unnecessarily re-downloaded.

---

Update History

Reads:

- Active League
- History Start Year
- Active Year

Checks what historical data is already stored and retrieves only missing or incomplete seasons.

---

Sheet Naming Rules

Use consistent uppercase names.

Preferred pattern:

- CONFIGURATION: START_HERE, LEAGUES
- RAW: *_RAW
- ANALYSIS: descriptive analysis names
- DISPLAY: descriptive user-facing names

Avoid creating sheets named after specific League IDs.

Avoid creating separate yearly sheets when Year can be stored as a column.

---

Data Integrity Rules

1. Preserve PlayerID as text.

2. Preserve FranchiseID as text if leading zeros are possible.

3. Preserve LeagueID consistently.

4. Never use player name as the only join key.

5. Never overwrite another league's data during refresh.

6. Never overwrite historical completed-season data without a deliberate rebuild.

7. Raw sheets should contain source data before presentation cleanup.

---

First Development Milestone

The first working version should remain small.

Phase 1:

1. START_HERE
2. LEAGUES
3. LEAGUE_INFO_RAW
4. PLAYERS_RAW
5. DRAFT_RAW
6. PlayerID-to-name join
7. DRAFT_POOL
8. DRAFT_BOARD

Only after that path works should the project add:

- ADP
- Historical scoring
- Player history
- Waiver analysis
- Lineup analysis
- Power rankings
- Additional dashboards

---

Development Philosophy

The workbook should always follow this pattern:

SOURCE
↓
RAW DATA
↓
ANALYSIS
↓
DISPLAY

If a display sheet is wrong, it should be possible to trace the problem backward to analysis and then to the original raw API response.

That traceability is a core design requirement of the project.
