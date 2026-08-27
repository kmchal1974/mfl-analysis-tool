MULTI_LEAGUE_MODEL

Purpose

This project is designed to analyze multiple MyFantasyLeague leagues from one shared system.

The tool must not be built around a single hard-coded league. Instead, League ID and season/year must be treated as core data dimensions throughout the project.

The expected scale is a small set of leagues, such as roughly a dozen, not hundreds.

---

Core Design Rules

1. Never hard-code a League ID in Apps Script, formulas, or analysis logic.

2. Never hard-code a season/year unless it is part of a test fixture.

3. Every league-specific record must include:
   
   - LeagueID
   - Year

4. Every player-specific record should preserve:
   
   - PlayerID

5. PlayerID should be treated as the primary identifier for a player whenever possible.

6. Player names are display values, not primary keys.

7. Global player information should be stored once and reused across leagues.

8. League-specific information must remain separated by LeagueID and Year.

9. Completed historical seasons should not be repeatedly downloaded unless there is a reason to refresh or rebuild them.

10. Current-season data may be refreshed as needed.

11. Authentication credentials must never be committed to the GitHub repository.

12. Raw API data should remain separate from analysis logic and display logic.

---

Primary Data Dimensions

The main dimensions used throughout the system are:

- LeagueID
- Year
- PlayerID
- FranchiseID
- Week
- PickNumber

Not every table needs every dimension.

---

Global Data

Global data is information that is not specific to one fantasy league.

Examples may include:

- PlayerID
- Player Name
- Position
- NFL Team
- Other player directory information
- Global ADP data, if the source is not league-specific

Example:

PlayerID| Name| Position| NFLTeam
12345| Example Player| WR| CHI

The global player table should not contain duplicate copies of the same player for every fantasy league.

---

League-Specific Data

League-specific data must include LeagueID and Year.

Examples include:

- League configuration
- Franchises
- Rosters
- Free agents
- Draft results
- Fantasy scoring
- Weekly results
- Lineups
- Standings
- Transactions
- League scoring rules

Example:

LeagueID| Year| FranchiseID| PlayerID
49286| 2026| 0001| 12345
71822| 2026| 0004| 12345

The same PlayerID may appear in multiple leagues because the player can be rostered differently in each league.

---

Historical Scoring Model

Historical scoring must remain league-specific because the same NFL player may score differently under different league scoring systems.

The preferred key for weekly player scoring is:

LeagueID + Year + Week + PlayerID

Example:

LeagueID| Year| Week| PlayerID| Points
49286| 2025| 1| 12345| 18.4
71822| 2025| 1| 12345| 22.9

This allows future analysis of:

- Year-over-year production
- Points per game
- Positional rank
- Consistency
- Multi-year averages
- Weighted historical averages
- Scoring-system differences
- Draft value versus past production

---

Draft Data Model

Draft data is league-specific.

Preferred key:

LeagueID + Year + PickNumber

Suggested fields:

- LeagueID
- Year
- PickNumber
- Round
- FranchiseID
- PlayerID
- DraftTimestamp, if available

Player names should be joined from the global player table using PlayerID.

---

Roster Data Model

Roster data is league-specific.

Preferred identifying fields:

LeagueID + Year + FranchiseID + PlayerID

Suggested fields:

- LeagueID
- Year
- FranchiseID
- PlayerID
- RosterStatus, if available
- Contract or salary information, if applicable

---

League Registry

The workbook should maintain a LEAGUES table containing the leagues available for analysis.

Suggested fields:

Enabled| LeagueID| LeagueName| CurrentYear| HistoryStart
TRUE| 49286| Example League| 2026| 2022

The LeagueName may eventually be populated automatically from MFL.

START_HERE should use this registry to select the active league.

---

Active League Concept

The workbook should have one currently selected league and season for display and analysis.

Example:

Active League: 49286
Active Year: 2026

Display sheets should react to the selected LeagueID and Year.

Changing the active league should not delete or overwrite stored data from other leagues.

---

Refresh Strategy

The project should support different refresh behaviors.

Current League Refresh

Refresh league-specific data for the selected LeagueID and Year.

Examples:

- Rosters
- Draft results
- Standings
- Transactions
- Current-season scoring

Draft Refresh

Refresh only the data necessary during a live draft.

Examples:

- Draft picks
- Current rosters
- Available-player pool

History Update

Check the requested historical range and retrieve only missing or incomplete seasons.

Completed historical seasons should normally be treated as cached data.

---

Authentication

Some MFL API calls may require authentication.

The project must assume authentication may be required.

Authentication credentials must:

- Never be committed to GitHub
- Never be stored directly in tracked .gs files
- Never appear in sample responses
- Never appear in public documentation

For Google Apps Script, credentials should be stored securely using Apps Script Properties or another secure mechanism.

---

Source of Truth

The repository is the source of truth for the project.

Important reference files include:

- docs/MFL_API_REFERENCE.md
- docs/MULTI_LEAGUE_MODEL.md
- docs/SHEET_ARCHITECTURE.md
- docs/DATA_MODEL.md
- docs/DECISIONS.md

Verified API behavior should be documented before dependent code is considered complete.

---

Development Principle

Build vertically rather than creating every future sheet at once.

Example development sequence:

1. START_HERE
2. LEAGUES
3. League information pull
4. PLAYERS_RAW
5. DRAFT_RAW
6. Draft player-name join
7. DRAFT_POOL
8. DRAFT_BOARD
9. Historical scoring
10. Additional analysis modules

Each stage should produce a working, testable result before the next stage is added.
