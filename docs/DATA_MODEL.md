DATA_MODEL

Purpose

This document defines how data is identified, stored, joined, and preserved throughout the MFL analysis tool.

The goal is to support:

- Multiple MyFantasyLeague leagues
- Multiple seasons
- Historical player analysis
- Draft analysis
- Future waiver, lineup, standings, and league-history modules

The model should remain stable even as new features are added.

---

Core Principles

1. League-specific data must include LeagueID and Year.

2. PlayerID is the primary identifier for players whenever possible.

3. FranchiseID is the primary identifier for league franchises.

4. Player names and franchise names are display values, not primary keys.

5. IDs should be stored as text unless there is a strong reason not to.

6. Raw API data should be preserved before analysis or display transformations.

7. Historical completed-season data should be retained rather than repeatedly rebuilt.

8. Tables should be designed so multiple leagues and multiple years can coexist.

---

Primary Identifiers

LeagueID

Represents a MyFantasyLeague league.

Example:

49286

Storage type:

TEXT

LeagueID must never be hard-coded into analysis logic.

---

Year

Represents the MFL season.

Example:

2026

Storage type:

INTEGER

Year should normally accompany LeagueID for league-specific data.

---

PlayerID

Represents an MFL player or MFL fantasy player entity.

Example:

12345

Storage type:

TEXT

PlayerID must remain text because MFL IDs may contain leading zeros.

PlayerID should be preserved even when player names are displayed.

---

FranchiseID

Represents a franchise within an MFL league.

Example:

0001

Storage type:

TEXT

FranchiseID must remain text because leading zeros may be significant.

FranchiseID is only meaningful within a specific LeagueID and Year context.

---

Week

Represents an NFL/fantasy scoring week.

Example:

1

Storage type:

INTEGER

---

PickNumber

Represents the overall draft pick number.

Example:

17

Storage type:

INTEGER

---

Table Definitions

PLAYERS_RAW

Purpose

Stores the global MFL player directory.

This table should not be duplicated by league.

Primary Key

PlayerID

Suggested Fields

Field| Type| Description
PlayerID| TEXT| MFL player identifier
Name| TEXT| Player display name
Position| TEXT| MFL position
NFLTeam| TEXT| NFL team abbreviation
PlayerType| TEXT| Individual player, team position, defense, etc. if determinable
Status| TEXT| Player status if supplied
LastUpdated| DATETIME| When this record was last refreshed

Notes

Do not use Name as a join key.

A player may change NFL teams without changing PlayerID.

Position or team information may change over time.

---

LEAGUES

Purpose

Registry of leagues configured for analysis.

Primary Key

LeagueID

Suggested Fields

Field| Type| Description
Enabled| BOOLEAN| Whether league is active in the system
LeagueID| TEXT| MFL league identifier
LeagueName| TEXT| Display name
CurrentYear| INTEGER| Current configured season
HistoryStart| INTEGER| Earliest season to retain
Host| TEXT| MFL host/server if needed
LastRefresh| DATETIME| Last successful refresh
Notes| TEXT| User/project notes

---

LEAGUE_INFO_RAW

Purpose

Stores league configuration returned by MFL.

Suggested Key

LeagueID + Year

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
LeagueName| TEXT
Host| TEXT
NumberOfFranchises| INTEGER
LeagueType| TEXT
RawUpdatedAt| DATETIME

Additional fields should be added only after verifying the MFL response.

---

FRANCHISES_RAW

Purpose

Stores franchise information.

Primary Key

LeagueID + Year + FranchiseID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
FranchiseID| TEXT
FranchiseName| TEXT
OwnerName| TEXT
DivisionID| TEXT
RawUpdatedAt| DATETIME

---

ROSTERS_RAW

Purpose

Stores roster ownership.

Suggested Key

LeagueID + Year + FranchiseID + PlayerID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
FranchiseID| TEXT
PlayerID| TEXT
RosterStatus| TEXT
Salary| NUMBER
ContractYear| TEXT
RawUpdatedAt| DATETIME

Fields such as Salary or ContractYear should only be populated if the league/API provides them.

---

DRAFT_RAW

Purpose

Stores draft selections.

Primary Key

LeagueID + Year + PickNumber

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
PickNumber| INTEGER
Round| INTEGER
PickInRound| INTEGER
FranchiseID| TEXT
PlayerID| TEXT
Timestamp| DATETIME
RawUpdatedAt| DATETIME

Notes

Player names should be joined from PLAYERS_RAW.

Do not duplicate player identity data unnecessarily.

---

PLAYER_SCORES_RAW

Purpose

Stores league-specific player scoring.

Primary Key

LeagueID + Year + Week + PlayerID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
Week| INTEGER
PlayerID| TEXT
Points| NUMBER
Starter| BOOLEAN
FranchiseID| TEXT
RawUpdatedAt| DATETIME

Notes

Points are league-specific because scoring rules differ.

The same PlayerID may have different Points in different leagues.

---

ADP_RAW

Purpose

Stores ADP data.

Suggested Key

Depends on the verified source.

If global:

Year + PlayerID

If league-specific:

LeagueID + Year + PlayerID

Suggested Fields

Field| Type
Year| INTEGER
PlayerID| TEXT
ADP| NUMBER
ADPRank| INTEGER
PositionADP| TEXT
Source| TEXT
LeagueID| TEXT
RawUpdatedAt| DATETIME

LeagueID should remain blank if the source is global.

---

FREE_AGENTS_RAW

Purpose

Stores MFL free-agent results for a league.

Suggested Key

LeagueID + Year + PlayerID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
PlayerID| TEXT
RawUpdatedAt| DATETIME

Notes

This table may later help verify availability logic.

Availability should still be modeled carefully because league settings may affect eligibility.

---

WEEKLY_RESULTS_RAW

Purpose

Stores weekly franchise matchup results.

Suggested Key

LeagueID + Year + Week + FranchiseID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
Week| INTEGER
FranchiseID| TEXT
OpponentID| TEXT
Score| NUMBER
OpponentScore| NUMBER
Result| TEXT
RawUpdatedAt| DATETIME

---

TRANSACTIONS_RAW

Purpose

Stores league transactions.

Primary Key

Use the MFL TransactionID if available.

Otherwise use a composite key such as:

LeagueID + Year + Timestamp + FranchiseID + PlayerID + TransactionType

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
TransactionID| TEXT
Timestamp| DATETIME
FranchiseID| TEXT
PlayerID| TEXT
TransactionType| TEXT
RawUpdatedAt| DATETIME

---

Derived Tables

Derived tables are not sources of truth.

They can be rebuilt from RAW tables.

---

DRAFT_POOL

Purpose

Combines player identity, draft state, roster state, ADP, and historical production.

Suggested Key

LeagueID + Year + PlayerID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
PlayerID| TEXT
Name| TEXT
Position| TEXT
NFLTeam| TEXT
Available| BOOLEAN
Drafted| BOOLEAN
Rostered| BOOLEAN
ADP| NUMBER
ADPRank| INTEGER
PreviousYearPoints| NUMBER
PreviousYearPPG| NUMBER
PreviousYearPosRank| INTEGER

---

PLAYER_HISTORY

Purpose

Stores summarized yearly player production.

Primary Key

LeagueID + Year + PlayerID

Suggested Fields

Field| Type
LeagueID| TEXT
Year| INTEGER
PlayerID| TEXT
Games| INTEGER
TotalPoints| NUMBER
PPG| NUMBER
PositionRank| INTEGER
Trend| NUMBER

This table is derived from PLAYER_SCORES_RAW.

---

Join Rules

Player Joins

Preferred join:

PlayerID

Example:

DRAFT_RAW.PlayerID
→
PLAYERS_RAW.PlayerID

Never rely on player name alone.

---

Franchise Joins

Preferred join:

LeagueID + Year + FranchiseID

Example:

DRAFT_RAW
→
FRANCHISES_RAW

using:

LeagueID
Year
FranchiseID

---

Score Joins

Preferred join:

LeagueID + Year + PlayerID

or for weekly detail:

LeagueID + Year + Week + PlayerID

---

Data Type Rules

IDs

Store as TEXT:

- LeagueID
- PlayerID
- FranchiseID
- TransactionID
- DivisionID

This protects leading zeros and avoids accidental numeric conversion.

---

Numeric Fields

Store as NUMBER or INTEGER:

- Year
- Week
- PickNumber
- Round
- Points
- ADP
- Games

---

Dates and Times

Store timestamps as true datetime values where practical.

Retain original timestamp information if MFL returns Unix time or another raw format.

---

Historical Data Rules

Completed seasons should normally be treated as immutable cached history.

Current-season data may be replaced or updated during refresh.

A history refresh should:

1. Determine requested LeagueID.
2. Determine HistoryStart.
3. Determine current requested Year.
4. Check which seasons already exist.
5. Pull only missing or incomplete seasons.
6. Refresh the active season if still in progress.

---

Duplicate Prevention

Before inserting RAW data, the script should know the table key.

Examples:

DRAFT_RAW
LeagueID + Year + PickNumber

PLAYER_SCORES_RAW
LeagueID + Year + Week + PlayerID

Existing rows with the same key should be updated or replaced according to the refresh strategy.

Duplicate records should not accumulate after repeated refreshes.

---

Data Provenance

Where useful, RAW tables should include:

RawUpdatedAt

Future tables may also include:

Source
Endpoint

This makes it easier to trace where data originated.

---

Null and Missing Values

Missing data should remain blank or null.

Do not automatically convert missing values to zero unless zero has a real meaning.

Examples:

Missing ADP:

blank

Not:

0

Missing points:

blank

unless MFL explicitly reports zero fantasy points.

---

Player Entity Types

MFL may contain player-like entities that are not individual human players.

Examples may include:

- Individual players
- Team defenses
- Team quarterbacks
- Team running backs
- Team wide receivers
- Team tight ends
- Team kickers

These should retain their own PlayerID values.

Do not assume every PlayerID represents an individual person.

Where possible, classify these using:

PlayerType

or the MFL position field.

---

Source of Truth Hierarchy

When troubleshooting data, use this order:

1. MFL documented API behavior
2. Saved sample API response
3. RAW sheet
4. Derived analysis table
5. Display sheet

The display layer should never be treated as the authoritative data source.

---

First Implementation Keys

For the initial draft module, the minimum required model is:

PLAYERS_RAW
Primary Key:
PlayerID

LEAGUE_INFO_RAW
Key:
LeagueID + Year

FRANCHISES_RAW
Key:
LeagueID + Year + FranchiseID

DRAFT_RAW
Key:
LeagueID + Year + PickNumber

DRAFT_POOL
Key:
LeagueID + Year + PlayerID

Additional tables should be added only when their data path is being actively implemented and tested.

---

Design Goal

Every piece of information should be traceable through:

SOURCE
↓
RAW RECORD
↓
KEY/JOIN
↓
ANALYSIS
↓
DISPLAY

The data model should make it possible to add new leagues, new seasons, and new analysis modules without redesigning the existing system.
