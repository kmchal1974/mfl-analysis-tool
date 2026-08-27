Welcome to the MyFantasyLeague.com Developers Program!
We want to thank you for your interest in the MyFantasyLeague.com Developers Program. The goal of this program is to provide the tools for our community to enhance the overall experience and enjoyment of the entire MFL user base. If you are a developer, these pages should provide you with all the information you need to create applications and other enhancements using the MFL platform.

New! For a list of changes made for the 2022 season, please check the Release Notes (last updated on July 25, 2022). 2020 Release Notes are here and 2019 Release Notes are here

Here is the overall information. You can get details on all the available calls on the Request Reference Page, view sample code or test the requests.

General Rules and Terms of Service
Access to this data is provided free to anyone to use in almost any way. However, the following uses are forbidden, whether deliberate or accidental:
Harvesting league and/or user data
Looking for loop holes or other ways to cheat or circumvent league rules
Overloading or disrupting the MFL service in any way that negatively impacts the normal operation of the site.
Collecting user information without their permission, attempting to make or making changes to someone\'s fantasy league/team without their permission.
Accessing the API via Javascript from web pages outside the myfantasyleague.com domain. Normal browser security will prevent this from working and we will not put your domain in our cross-domain file to allow it.
Access to this data is provided with the assumption that all developers will make their best effort to comply with the best practices listed here. Accidental or deliberate abuse of the service will not be tolerated.
Your application will perform much better if you cache data whenever possible. For example, our player database is only changed once a day, so your application should request that info no more than once a day and keep it for that long. You should do the same with league data that doesn't change often, like league rules and scoring systems.
This service is offered as is, without any guarantees expressed or implied. This service may be occassionally unavailable, or change or may be discontinued at any time, without warning.
If we determine that someone is abusing the system, whether deliberately or accidentally we may reject those requests and/or take whatever action we deemed necessary. Just because we allowed it in the past is no guarantee that we will allow it in the future.
We will do our best to answer your question regarding our API's. However we can't explain how to make API calls from your environment, or how to parse the results or any other details of how to use this data. Some help is available on the Sample Code link on the main API documentation page
We can not and will not under any circumstance make raw NFL player stats available, as that's forbidden per our stats licensing agreement. Similarly, we can not make third party content (such as player news) available. If you are interested in getting raw NFL stats, here are some affordable options:
FantasyData.com
Sportsradar
XML Team
These terms may be changed at any time. Use of the service constitutes agreement to these terms. We reserve the right to take whatever action we feel is warranted whenever anyone violates these terms.
Basic Information
In order to properly use the service you need to understand a few things about the requests you will be making, speficially the URL format. All requests URL will be of the form:

protocol://host/year/command?args
For example, in the request:

https://www44.myfantasyleague.com/2026/standings?L=80000&W=3

the elements of the request are:

protocol: https
host: www44.myfantasyleague.com
year: 2026
command: standings
args: L=80000&W=3
Each of these will be described below in more detail.

The protocol can be http or https. MFL started to support the https protocol in 2017 and at this time most requests can be made via either protocol. It is possible that in the next year we will require all API calls to be made in https so you may want to start planning for that.

The host is the server where the league you are accessing lives. Our hosts at this time are of the form wwwXX where XX is a 2-digit number. However that's likely to change in the near future. When you ask for a league information (via the league API) it will return the league's host. Similarly, when getting all the leagues a user has (the myleague call) or using the league search functionality (the leagueSearch call), you will also receive the host information. You should save the league's host for period of time, but note that leagues can be moved between hosts. Moving leagues is unusual but does happen, specially if a server goes down or as we get ready for the start of the football season and need to balance load across hosts. Best practice is to get the data and cache it for one session. Note that if you send a request to the wrong host, the system will normally redirect the request to the proper server, but this operation may be delayed, or if the system is under too much stress, it may be denied. In addition, if the request does not take a league parameter (L=), it must be sent to the host api.

The year is fairly straight-forward. It refers to the season you are requesting data for. Note that January 2018 will still be part of the 2017 season, so this is not the current year. Also, most API functionality is only supported for the current year. We will not be adding features or fixing issues related to queries that refer to past seasons.

The command specifies what direction the data flows. Most requests will fall into two categories: those requesting data and those that will attempt to load data into the system. These requests use the commands export and import respectively. However, as listed on the Request Reference Page, there are other commands that serve more specific purposes.

The args specifies the additional arguments for the request. The export and import commands require a request type, specified via the TYPE argument. This indicates the actual data you want back or want to update.

All requests that operate on a league require a league argument (L). The second most common one is the week argument (W), which is a number between 1 and 21. And finally the JSON argument should be set to 0 or 1 depending if you want the output in XML (default) or JSON format. Additional request parameters are listed in the Request Reference Page.

User Agent And Client Registration
Starting in 2020 we will be monitoring and restricting usage of the API. This has become necessary because a number of bad actors are abusing the system. While most of these cases are likely just users not knowing any better, a few have been pretty clearly done with malicious intent.

The API will remain open as is always been. However, unregistered clients will be limited in the amount of requests they can make. The actual limits will depend on a number of factors and won't be constant. The limits will only apply on a per-IP address basis. So if your client is spread across many users, it won't be affected by this. Thus mobile apps and calls from within league pages should not affected by this.

Registered clients will have higher limits, about 2.5X of the limits for un-registered clients. To have your client receive the higher limits, you must:

Register your client via API Client Registration Page.
Validate the client (this means that we will send a text to your cell phone number of record with a code on it that you will then enter on the site)
Pass the User-Agent you chose in the Client Registration on all API requests your client makes.
When you go over the limit, we will start throttling some requests from your client. The more you go over the limit, the more requests will be throttled. Throttled requests will return a 429 "Too Many Requests" HTTP status code. Here's some suggestions on what to do if you are hitting the limit:

Space your requests out. This is the most important thing you can do. Wait one second between making requests and you should be ok. We are not publishing what the limits are as they are not fixed and will vary depending on a lot of factors. For performance reasons we are not counting all requests, just a sample of them. So the limits won't ever be exact.
Cache the data so you don't have to request it as often. Some requests like players doesn't change often, so don't request it often.
Don't make unnecessary calls. Make calls depending on the point we are on the season. Asking for stats and results for week 5 during week 3 makes no sense and yet there are a lot of such calls being made. Also understand the league parameters. Don't ask for a league draft results when they do an auction.
Avoid making calls during high-traffic times. For instance during the NFL season we may lower the thresholds while games are going on. We may increase them during the late night/early morning hours.
If the requests are not league-specific, use api.myfantasyleague.com as the host. That will spread out your requests across a number of servers. The limits are applied on a per server basis.
If a request fails, don't retry. That will make things worse. If you can, check for the 429 status and if you get it, cool things down a bit.
This is not a perfect system and if it turns out is not enough of a deterrent for these bad actors we will need to take stronger measures. We hope it doesn't come to that.

Other Arguments And Values
Players

All requests involving players identify players by their player id. Our player ids are 4 or 5 digit numbers, but are treated as strings internally. Ids under 1000 need a leading 0 (so the player id for the Baltimore Ravens defense is "0531", not "531"). If you treat them as integers you will run into issues.

Franchises

Some requests allow you to specify a franchise id. Franchise ids are always 4-digit numeric strings, with leading zeros, starting at "0001". Like player ids, franchise ids are strings, not numbers, so please make sure to treat them as such. In some cases the value "0000" may be passed in to indicate a commissioner operation.

Timestamps

All timestamps are specified using the standard Unix time format. Your application needs to be able to convert times in this format to a user-readable form. The timestampts are always specified in the EST/EDT timezone.

Import Data

Some import requests require you to pass in an XML representation of the data being uploaded, via a field called 'DATA'. In most cases, you will need to properly encode this data so that special characters on it don't cause issues. It is also recommended that you use a POST request in these cases as that would make things easier in the long run. GET requests have length limits that can be exceeded very easily. With XML data is imperative that you get the formatting right as well as the various element and attribute names. The data being imported would always be in the same format as the corresponding export data.


Log In And Authorization
In order to access some league data, and to make any changes, your request must be properly authorized to access and/or change this data. Authorization is handled via cookies. Your request must pass in the cookie of the user who is performing the operation. Access will then be granted depending on the user's league privileges. Data from leagues that are marked as PRIVATE are no longer accessible by users not in the league.

To pass the proper user cookie, you must first programatically log the user in. You do this via the login API. The process is as follows:

Prompt for the customer's username and password - you might want to do this similarly to how we do it on the login page.
Programmatically call the login API at: https://api.myfantasyleague.com/2026/login?USERNAME=testuser&PASSWORD=testing&XML=1, passing in a valid username and password. You must use HTTPS for this request to avoid exposing the user's credentials. For ease of illustrating this example, the request was shown using the GET method. For better security we recommend you call it using the POST method. We may stop supporting the GET method for login at any time.
If valid information is passed into the login program, the response will include a <status cookie_name="cookie_value"...>.
If invalid information is passed into the login program, it will respond with an <error... status message.
The above cookie name/value pair should be passed back in as via a standard HTTP header cookie in the format: "Cookie: MFL_USER_ID=cookie_value" in all subsequent calls to the API.
Note that the cookie value is a Base64 string. That means it may contain the special symbols '+', '/' and/or '='. Depending on your environment, you may need to explicitly URL-escape these symbols before passing them back to us (e.g. converting '=' to '%3D').
Note that some requests require commissioner access. For these requests you must pass the cookie of a user with commissioner access in that league. Requests that do not require commissioner access, when requested by a user who has commissioner access will be performed on the commissioner's franchise. Some requests (not all) can also be performed on behalf of another franchise by passing the FRANCHISE_ID parameter. If the commissioner does not have a franchise and no franchise id is given, it will return an error.

To log a user out, all the client needs to do is discard the cookie, or stop sending it in the API requests. There's no need to inform the server that this has ocurred so there's no corresponding logout API call.

Because sometimes is not possible to collect the user's credentials when making API requests we provide an alternate way to authorize requests. Note that for security reasons, this alternate method does not work for import requests, only export. It also does not work for requests that require Commissioner access (i.e. it's only valid for owners). To use it, you may pass to any request that requires both a league id and is access-restricted (i.e. requires a user cookie), the APIKEY parameter. The value for this parameter is user specific. It may be accessed via javascript on any page on the site when a user is logged in via the variable apiKey. Note that this API key is tied to a user/franchise/league combination and does not work outside that context. If you pass both a cookie and the APIKEY parameter, the APIKEY parameter will take precedence. This parameter is ignored on requests that don't require authorization.

You may also get your own API key and pass that on requests to your league. Since you are logged in on this league, your API key to access league 14414 is "aRFj3siSvuWpx0+mOV3FYDYeHbox" (the key is everything inside the quotes not including the quotes). Do not share or make this info public as that may compromise the security of your league. But you may use this in your own personal programs for your own use. This key is good for the entire season but if you feel is compromised you can contact us and we can invalidate it and assign you a new one.

MyFantasyLeague.com Developers Program API Reference
Before you attempt to use this API, you should first read the General Information Page. You can also view sample code or test the requests.

Show requests for command: Export

These requests are invoked using the format protocol://host/year/export?TYPE=request_type&additional_args

Export Requests
Request Type	Description	Arguments
Common League Info
league	General league setup parameters for a given league, including: league name, roster size, IR/TS size, starting and ending week, starting lineup requirements, franchise names, division names, and more. If you pass the cookie of a user with commissioner access, it will return otherwise private owner information, like owner names, email addresses, etc. Test it!
Personal user information, like name and email addresses only returned to league owners.	
L	League Id(required)
rules	League scoring rules for a given league. To understand the scoring rule abbreviations in this document, see the allRules document type above. Test it!	
L	League Id(required)
rosters	The current rosters for all franchises in a league, including player status (active roster, IR, TS), as well as all salary/contract information for that player. Test it!	
L	League Id(required)
FRANCHISE	When set, the response will include the current roster of just the specified franchise.
W	If the week is specified, it returns the roster for that week. The week must be less than or equal to the upcoming week. Changes to salary and contract info is not tracked so those fields (if used) always show the current values.
freeAgents	Fantasy free agents for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
POSITION	Return only players from this position.
schedule	The fantasy schedule for a given league/week. Weeks in the past will show the score of each matchup. Test it!
Private league access restricted to league owners.	
L	League Id(required)
W	Week. If a week is specified, it returns the fantasy schedule for that week, otherwise the full schedule is returned.
F	Franchise ID. If a franchise id is specified, the schedule for just that franchise is returned.
calendar	Returns a summary of the league calendar events. Test it!
Access restricted to league owners.	
L	League Id(required)
playoffBrackets	All playoff brackets for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
playoffBracket	Returns the games (with results if available) of the specified playoff bracket. Test it!
Private league access restricted to league owners.	
L	League Id(required)
BRACKET_ID	The bracket id to return the info
Transactions
transactions	All non-pending transactions for a given league. Note that this can be a very large set, so it's recommended that you filter the result using one or more of the available parameters. If the request comes from an owner in the league, it will return the pending transactions for that owner's franchise. If it comes from the commissioner, it will return all pending transactions. Test it!
Private league access restricted to league owners.	
L	League Id(required)
W	If the week is specified, it returns the transactions for that week.
TRANS_TYPE	Returns only transactions of the specified type. Types are: WAIVER, BBID_WAIVER, FREE_AGENT, WAIVER_REQUEST, BBID_WAIVER_REQUEST, TRADE, IR, TAXI, AUCTION_INIT, AUCTION_BID, AUCTION_WON, SURVIVOR_PICK, POOL_PICK. You may also specify a value of * to indicate all transaction types or DEFAULT for the default transaction type set. Or you can ask for multiple types by separating them with commas.
FRANCHISE	When set, returns just the transactions for the specified franchise.
DAYS	When set, returns just the transactions for the number of days specified by this parameter.
COUNT	Restricts the results to just this many entries. Note than when this field is specified, only transactions from the most common types are returned.
pendingWaivers	Pending waivers that the current franchise has submitted, but have not yet been processed. Test it!
Access restricted to league owners.	
L	League Id(required)
FRANCHISE_ID	When request comes from the league commissioner, this indicates which franchise they want.
pendingTrades	Pending trades that the current franchise has offered to other franchises, or has been offered to by other franchises. Test it!
Access restricted to league owners.	
L	League Id(required)
FRANCHISE_ID	When request comes from the league commissioner, this indicates which franchise they want. Pass in '0000' to get trades pending commissioner action).
tradeBait	The Trade Bait for all franchises in a league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
INCLUDE_DRAFT_PICKS	When set, this will also return draft picks offered. Current year draft picks look like DP_02_05 which refers to the 3rd round 6th pick (the round and pick values in the string are one less than the actual round/pick). For future years picks, they are identified like FP_0005_2018_2 where 0005 referes to the franchise id who originally owns the draft pick, then the year and then the round (in this case the rounds are the actual rounds, not one less). This also includes Blind Bid dollars (in leagues that use them), which will be specified as BB_10 to indicate $10 in blind bid dollars.
assets	All tradable assets (players, current year draft picks, future draft picks) for a given league. Test it!
Access restricted to league owners.	
L	League Id(required)
Scoring and Results
leagueStandings	The current league standings for a given league. The fields returned for each franchise match the columns included in the league standings report. Test it!
Private league access restricted to league owners.	
L	League Id(required)
COLUMN_NAMES	When set to a non-zero value, returns a mapping of column keys to column names. This also shows the proper order of the standings columns.
ALL	When set to a non-zero value, returns additional standings field values that are not part of the league standings report but are important for sorting league standings. Note that some of these extra fields may not be relevant for all leagues and may include duplicate info from the basic set.
WEB	When set to a non-zero value, returns the columns shown on the web site only. This is in case the app wants to just replicate the report from the web site. This parameter is ignored if ALL is set.
weeklyResults	The weekly results for a given league/week, including the scores for all starter and non-starter players for all franchises in a league. The "W" parameter can be "YTD" to give all year-to-date weekly results. Test it!
Private league access restricted to league owners.	
L	League Id(required)
W	If the week is specified, it returns the data for that week, otherwise the most current data is returned. If the value is 'YTD', then it returns year-to-date data (or the entire season when called on historical leagues).
MISSING_AS_BYE	If set to 1, fantasy teams with no scheduled opponents will be shown as playing vs a BYE opponent.
liveScoring	Live scoring for a given league and week, including each franchise's current score, how many game seconds remaining that franchise has, players who have yet to play, and players who are currently playing. Test it!	
L	League Id(required)
W	If the week is specified, it returns the data for that week, otherwise the most current data is returned.
DETAILS	Setting this argument to 1 will return data for non-starters as well
playerScores	All player scores for a given league/week, including all rostered players as well as all free agents. Test it!
Private league access restricted to league owners.	
L	League Id(required)
W	If the week is specified, it returns the data for that week, otherwise the current week data is returned. If the value is 'YTD', then it returns year-to-date data. If the value is 'AVG', then it returns a weekly average.
YEAR	The year for the data to be returned.
PLAYERS	Pass a list of player ids separated by commas (or just a single player id) to receive back just the info on those players.
POSITION	Return only players from this position.
STATUS	If set to 'freeagent', returns only players that are fantasy league free agents.
RULES	If set, and a league id passed, it re-calculates the fantasy score for each player according to that league's rules. This is only valid when specifying the current year and current week.
COUNT	Limit the result to this many players.
projectedScores	Given a player ID, calculate the expected fantasy points, using that league's scoring system. The system will use the raw stats that fantasysharks.com projects. Test it!
Private league access restricted to league owners.	
L	League Id(required)
W	If the week is specified, it returns the projected scores for that week, otherwise the upcoming week is used.
PLAYERS	Pass a list of player ids separated by commas (or just a single player id) to receive back just the info on those players.
POSITION	Return only players from this position.
STATUS	If set to 'freeagent', returns only players that are fantasy league free agents (note that this refers to players that current free agents, not that were free agents during the specified week).
COUNT	Limit the result to this many players.
Draft & Auction
draftResults	Draft results for a given league. Note that this data may be up to 15 minutes delayed as it is meant to display draft results after a draft is completed. To access this data while drafts are in progress, check out this FAQ. Test it!
Private league access restricted to league owners.	
L	League Id(required)
auctionResults	Auction results for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
selectedKeepers	Currently selected keepers. Returns only own's franchise keepers. For commissioner returns all franchises unless Lockout is on. Test it!
Private league access restricted to league owners.	
L	League Id(required)
FRANCHISE	When set, returns just the keepers for the specified franchise (only for commissioner).
myDraftList	My Draft List for the current franchise. Test it!
Access restricted to league owners.	
L	League Id(required)
Communications
messageBoard	Display a summary of the recent message board posts to a league message board. Test it!
Access restricted to league owners.	
L	League Id(required)
COUNT	If specified, limit the number of threads to display to this value. Default is 10.
messageBoardThread	Display posts in a thread from a league message board. Test it!
Access restricted to league owners.	
L	League Id(required)
THREAD	Thread id (required).
polls	All current league polls with details as to which polls the current franchise voted on. Test it!
Access restricted to league owners.	
L	League Id(required)
device_tokens	Returns the device tokens for the current user. Test it!	 
League Players
playerRosterStatus	Get the player's current roster status. The franchise(s) the player is on are listed in the subelement. There may more than one of this for leagues that have multiple copies of players. Each of this elements will have a franchise id and status attribute. The status attribute can be one of: R (roster), S (starter), NS (non-starter), IR (injured reserve) or TS (taxi squad). The R value is only provided when there's no lineup submitted or the caller has no visibility into the lineup. If the player is a free agent, there will be a 'is_fa' attribute on the parent element. In those cases the elements 'cant_add' and 'locked' attributes may be set indicating whether a player can't be added or is locked. Test it!	
L	League Id(required)
P	Player id or list of player ids separated by commas (required).
W	Week. If a week is specified, it returns the player status for that week. The default is the current Live Scoring week.
F	Franchise it. If present it uses the franchise id to determine which conference or division to use for the purposes of identifying free agents. If not present it uses the user's franchise id. Only matters on deluxe leagues.
myWatchList	My Watch List for the current franchise. Test it!
Access restricted to league owners.	
L	League Id(required)
contestPlayers	On Contest Leagues, this returns all players eligible to be in a franchise's starting lineup. While this request can be used by any league it's best suited for leagues with the loadRosters setting set to either 'contest' or 'setem'. Test it!
Access restricted to league owners.	
L	League Id(required)
W	Week. If a week is specified, it returns the players for that week. The default is the upcoming week.
F	Franchise it. If present it returns the players eligible for that franchise. Only matters when called by the commissioner. When called by an owner, this parameter is ignored and the owner's franchise is used.
salaries	The current player salaries and contract fields. Only players with values are returned. If a value is empty it means that the default value is in effect. The default values are specified under the player id '0000'. Test it!
Private league access restricted to league owners.	
L	League Id(required)
salaryAdjustments	All extra salary adjustments for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
Other League Info
futureDraftPicks	Future draft picks for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
accounting	Returns a summary of the league accounting records. In the response, negative amounts are charges against the franchise while positive amounts is money paid by the franchise or owed to the franchise. Test it!
Private league access restricted to league owners.	
L	League Id(required)
pool	All NFL or fantasy pool picks for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
POOLTYPE	Which pool picks to return. Valid values are "NFL" (default) or "Fantasy".
survivorPool	All survivor pool picks for a given league. Test it!
Private league access restricted to league owners.	
L	League Id(required)
abilities	Returns the abilities of the current franchise. A value of 0 means the franchise does not have the ability and a value of 1 means it has the ability. Test it!
Access restricted to league owners.	
L	League Id(required)
F	Franchise ID. When the request comes from the commissioner, this indicates which franchise's abilities to return. It's ignored if the request comes from an owner.
DETAILS	If set to a non-zero/empty value, includes the abilities descriptions.
appearance	The skin, home page tabs, and modules within each tab set up by the commissioner for a given league. Test it!	
L	League Id(required)
rss	An RSS feed of key league data for a given league, including: league standings, current week's live scoring, last week's fantasy results, and the five newest message board topics. Test it!
Private league access restricted to league owners.	
L	League Id(required)
ics	Returns a summary of the league calendar in .ics format, which is suitable for importing into many modern calendaring programs, like Apple's Calendar, Google Calendar, Microsoft Outlook, and more. Test it!
Access restricted to league owners.	
L	League Id(required)
User Functions
myleagues	All of the leagues of the current user. Test it!
Private league access restricted to league owners.
Personal user information, like name and email addresses only returned to league owners.	
YEAR	Returns the data for the specified year. Default is current year.
FRANCHISE_NAMES	Set this argument to 1 to include the franchise names in the response. Note that when this parameter is set, and the user has a lot of leagues, this response may take a long time to process and time out.
leagueSearch	Searches for leagues in our database that match either the given league id or whose name matches the specified string. Either the SEARCH or ID paramater must be specified, but not both. Test it!	
SEARCH	Case-insensitive string to search for. Must be at least 3 characters long.
ID	4 or 5-digit number to return the league with this id.
YEAR	Year to search, default is the year in the URL.
Fantasy Content
players	All player IDs, names and positions that MyFantasyLeague.com has in our database for the current year. All other data types refer only to player IDs, so if you'd like to later present any data to people, you'll need this data type for translating player IDs to player names. Our player database is updated at most once per day, and it contains more than 2,000 players - in other words, you're strongly encouraged to read this data type no more than once per day, and store it locally as needed, to optimize your system performance. Test it!	
L	League Id(optional)
DETAILS	Set this value to 1 to return complete player details, including player IDs from other sources.
SINCE	Pass a unix timestamp via this parameter to receive only changes to the player database since that time.
PLAYERS	Pass a list of player ids separated by commas (or just a single player id) to receive back just the info on those players.
playerProfile	Returns a summary of information regarding a player, including DOB, ADP ranking, height/weight. Test it!	
P	Player id or list of player ids separated by commas (required).
allRules	All scoring rules that MyFantasyLeague.com currently supports, including: if the rule is scored for players, teams or coaches, as well as an abbreviation of the scoring rule, a short description, and a detailed description. If you plan on using the 'rules' data type, you'll also need this data type to look up the abbreviations to translate them to their detailed description for people. Test it!	 
playerRanks	This report provides overall player rankings from the experts at FantasySharks.com. These rankings can be used instead of Average Draft Position (ADP) rankings for guidance during your draft, or when generating your own draft list. Test it!	
POS	Return only players at this position.
SOURCE	Which player ranks source to use, default is 'sharks')
adp	ADP results, including when the result were last updated, how many drafts the player was selected in, the average pick, minimum pick and maximum pick. Test it!	
PERIOD	This returns draft data for just drafts that started after the specified period. Valid values are ALL, RECENT, DRAFT, JUNE, JULY, AUG1, AUG15, START, MID, PLAYOFF. This option is not valid for previous seasons.
FCOUNT	This returns draft data from just leagues with this number of franchises. Valid values are 8, 10, 12, 14 or 16. If the value is 8, it returns data from leagues with 8 or less franchises. If the value is 16 it returns data from leagues with 16 or more franchises.
IS_PPR	Filters the data returned as follows: If set to 0, data is from leagues that not use a PPR scoring system; if set to 1, only from PPR scoring system; if set to -1 (or not set), all leagues.
IS_KEEPER	Pass a string with some combination of N, K and R: if N specified, returns data from redraft leagues, if 'K' is specified, returns data for keeper leagues and if 'R' is specified, return data from rookie-only drafts. You can combine these. If you specify 'KR' it will return rookie and keeper drafts only. Default is 'NKR'.
IS_MOCK	If set to 1, returns data from mock draft leagues only. If set to 0, excludes data from mock draft leagues. If set to -1, returns all
CUTOFF	Only returns data for players selected in at least this percentage of drafts. So if you pass 10, it means that players selected in less than 10% of all drafts will not be returned. Note that if the value is less than 5, the results may be unpredicatble.
DETAILS	If set to 1, it returns the leagues that were included in the results. This option only works for the current season.
aav

MyFantasyLeague.com Developers Program API Reference
Before you attempt to use this API, you should first read the General Information Page. You can also view sample code or test the requests.

Show requests for command: Import

These requests are invoked using the format protocol://host/year/import?TYPE=request_type&additional_args

Import Requests
Request Type	Description	Arguments
Common League Info
lineup	Import a franchise's starting lineup. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
W	The week you are setting the lineup for (required).
STARTERS	A comma-separated list of players to use as the starters (required).
COMMENTS	A short message to be save as the comments for this starting lineup (optional).
TIEBREAKERS	For leagues that use tiebreaker player(s), specify them via this field.
BACKUPS	For leagues that use backup player(s), specify them via this field. Note that this feature is no longer supported.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
franchises	Loads franchise names, graphics, contact information, and more. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
DATA	XML string representing the franchises' data. Data format is the same as the contents of <franchises> element in the export league API. See Import Data details.
OVERLAY	If this parameter is set, the passed in data will overlay the existing data. Fields not uploaded will be left as is. If this parameter is not set, all the data not passed in will be erased.
calendarEvent	Import an event to a league calendar. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
EVENT_TYPE	The id of the event type being added to the calendar. The more commont event types are "DRAFT_START", "AUCTION_START", "TRADE" (for trade deadline), "WAIVER_REVERSE", "WAIVER_BBID", "WAIVER_UNLOCK", "WAIVER_LOCK", and "CUSTOM" (required).
START_TIME	The Unix time of when the event starts (required).
END_TIME	The Unix time of when the event ends (optional).
HAPPENS	If specified, the event is added at the same time each week for the following this many weeks.
Transactions
fcfsWaiver	Import an add/drop move that will be executed immediately (also known as a first-come, first-serve move). You may also use this call to drop multiple players at once. At least one player to be added or dropped is required. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
ADD	The player id to add.
DROP	A comma-separated list of player ids to drop.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
waiverRequest	Import one round's worth of waiver requests. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
ROUND	Specifies the waiver round this request is for (required).
PICKS	A comma delimited list of player waiver claims. Each claim is two player ids separated by an underscore. The first player id is the player to ask for and the second is the one to drop if the waiver claim is awarded. For example, the value "1111_2222,3333_4444" means that the first priority is to acquired player id 1111 dropping 2222, but if that's not possible, add 3333 and drop 4444 (required). In order to clear the whole round, pass an empty value for PICKS.
REPLACE	If there are already picks for that round, the ones specified via the PICKS parameter are added to the existing request unless this parameter is set in which case it replaces the current entries.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
blindBidWaiverRequest	Import blind bidding waiver requests. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
ROUND	Specifies the waiver round this request is for (required only if the league uses conditional blind bidding).
PICKS	A comma delimited list of bids. Each bid consists of the player id to bid on, the bid amount and the player to drop if the claim is awarded, separated by underscores. For example, the value "1111_5_2222,3333_2_4444" means that the first priority is to acquired player id 1111 for $5, dropping 2222, but if that's not possible, add 3333 for $2 and drop 4444 (required). Specify "0000" as the player id when not dropping a player. In order to clear the whole round, pass an empty value for PICKS.
REPLACE	If there are already picks for that round, the ones specified via the PICKS parameter are added to the existing request unless this parameter is set in which case it replaces the current entries.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
ir	Import an IR (activate/deactivate) move. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
ACTIVATE	Comma-separated list of player ids to activate (move from Injured Reserve to Active Roster).
DEACTIVATE	Comma-separated list of player ids to deactivate (move from Active Roster to Injured Reserve).
DROP	Comma-separated list of player ids to drop from the roster. This applies to all players on the roster, regardeless of roster status.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
taxi_squad	Import a Taxi Squad (promote/demote) move. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
PROMOTE	Comma-separated list of player ids to promote (move from Taxi Squad to Active Roster).
DEMOTE	Comma-separated list of player ids to demote (move from Active Roster to Taxi Squad).
DROP	Comma-separated list of player ids to drop from the roster. This applies to all players on the roster, regardeless of roster status.
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
tradeProposal	Propose a trade to another franchise. The WILL_GIVE_UP and WILL_RECEIVE parameters can also contain draft picks if the league allows draft pick trading. Current year draft picks are specified like DP_02_05 which refers to the 3rd round 6th pick (the round and pick values in the string are one less than the actual round/pick). For future years picks, they are identified like FP_0005_2018_2 where 0005 referes to the franchise id who originally owns the draft pick, then the year and then the round (in this case the rounds are the actual rounds, not one less). If the league uses Blind Bidding and allows trading of blind bid dollars, you can specify like BB_10.50, which means $10.50 worth of blind bid dollars. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
OFFEREDTO	Target franchise id of the trade proposal (required).
WILL_GIVE_UP	Comma-separated list of player ids or other assets (see description) being offered (required).
WILL_RECEIVE	Comma-separated list of player ids or other assets (see description) being asked for (required).
COMMENTS	Short message to send to the target of the trade proposal (optional).
EXPIRES	Unix time specifying when the trade proposal expires (default is one week from when offered).
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
tradeResponse	Respond to an existing trade offer. See the tradeProposal request above for info regarding draft pick trading. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
TRADE_ID	Trade id as returned by the pending Trades export API (required).
RESPONSE	Whether the trade proposal is accepted or rejected. Valid values are 'accept', 'reject' or 'revoke' (required). The 'revoke' response is only allowed if the request is made by the originator of the trade proposal. The other two options are allowed only if the request is made by the target of the trade proposal.
COMMENTS	Short message to send to the originator of the trade proposal when the trade is rejected (optional).
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
tradeBait	Import an owner's trade bait, which will overwrite his previously entered trade bait. Test it!
Access restricted to league owners.	
L	League Id(required)
WILL_GIVE_UP	Comma-separated list of player ids being offered (required). Draft picks are also allowed assuming the league supports draft pick trading (see tradeProposal request for details on how to format them).
IN_EXCHANGE_FOR	A description of what the team is interested in getting back in a trade. This can be a string of no more than 256 characters. Spaces and all other special characters should be properly escaped.
Draft & Auction
draftResults	Loads draft order and actual draft results. All previously existing draft results will be completely deleted from the system when importing draft results. Note that this is meant to be used to load the results of an offline draft all at once, not to implement a live draft application. An additional attribute called 'status' is supported on each draft pick element, allowing you to indicate the status of the player upon import. The valid values are: ROSTER, TAXI_SQUAD, or INJURED_RESERVE. If no status is indicated, it defaults to ROSTER. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
DATA	XML string representing the draft results data. See Import Data details.
auctionResults	Auction results. This is meant to be used to load the results of an offline draft all at once, not to implement a live draft application. An additional attribute called 'status' is supported on each auction element, allowing you to indicate the status of the player upon import. Valid values are: ROSTER, TAXI_SQUAD, or INJURED_RESERVE. If no status is indicated, it defaults to ROSTER. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
DATA	XML string representing the auction results data. See Import Data details.
CLEAR	If set to 1, it will clear out any current auction results. This is a cosmetic info only, that affects what's displayed in the Action Results report but leaves the current rosters as they area.
OVERWRITE	If set to 1, it will overwrite the current rosters with the new ones. The default is to append the new players to existing rosters. Note that if set it may result in multiple copies of the same player. Note that setting OVERWRITE=1 without setting CLEAR=1 may result in an inconsistent state regarding available funds.
myDraftList	Set the players in an owner's My Draft List. This will completely overwrite an owners previous My Draft List. Test it!
Access restricted to league owners.	
L	League Id(required)
PLAYERS	Comma-separated list of player IDs to set the list to (required).
Communications
messageBoard	Start a message board thread or reply to an existing message board thread. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
FRANCHISE_ID	The franchise id posting this message. Only valid when the request comes from the league commissioner. Use "0000" as the value to indicate the posting should be from the Commissioner, not an owner.
THREAD	Message board thread id. If set it assumes the post is a reply to this thread.
SUBJECT	When starting a new thread (i.e. ehe THREAD parameter is not specified), this parameter specifies the subject of the thread.
BODY	The body of the message (required).
pollVote	Import a vote in a league poll. Test it!
Access restricted to league owners.	
L	League Id(required)
POLL_ID	The poll id that the vote is for (required).
ANSWER_ID	The id of the selected poll choice (required). If the poll allows multiple answers, separate them with commas.
emailMessage	Send an email message to one or all the owners in the league. Test it!
Access restricted to league owners.	
L	League Id(required)
SEND_TO	If specified, send the message just to this franchise. Leave this parameter out if you want the email to go to all league owners.
SUBJECT	Specifies the subject of the email message (required).
BODY	Specifies the body of the email message (required).
INVITE	If set, this causes the franchise-specific league invite link to be included in each email message body.
add_device_token	Registers a device token for the given user. Used to send notifications via a mobile app. Accessed restricted to logged in users. Test it!	
TOKEN	The device token to add.
NAME	The name for this device, must be less than 36 characters.
DELETE	If set to ALL, it clears out all the tokens for this user, otherwise it deletes the token with the given name.
League Players
salaries	XML string representing the player salaries. The format for this data is
<salaries>
<leagueUnit unit="LEAGUE">
<player id="9823" salary="11" contractStatus="A" contractYear="1"
        contractInfo="info" />
<player id="8670" salary="7.56" contractYear="2" contractInfo="more info" />
...
</leagueUnit>
</salaries>
The valid attributes in the player elements depend on the league salary settings. If a setting is turned off in the Salary Cap Setup page, it will not be imported. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
DATA	XML string representing the league salaries data. See Import Data details.
APPEND	If this parameter is set to any non-zero value, the passed in data will overlay the existing data. Salaries not uploaded will be left as is. If this parameter is not set, the salaries not passed in will be erased..
salaryAdj	XML string representing the salary adjustments. The format for this data is
<salary_adjustments>
<salary_adjustment franchise_id="0001" amount="5.75"
                   explanation="$5.75 fine for being late to draft."/>
<salary_adjustment franchise_id="0007" amount="-3.00" 
                   explanation="$3 credit for some reason."/>
...
</salary_adjustments>
For all adjustments, the franchise_id, amount and explanation fields are required and must not be empty. Use a negative amount to credit the franchise (i.e. reduce their salary). The data will always be added to the existing salary adjustments. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
DATA	XML string representing the salary adjustments to be made. See Import Data details.
playerScoreAdjustment	Import one player score adjustment record. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
PLAYER	The player id whose score is being adjusted (required).
WEEK	The week number of the score being adjusted (required).
POINTS	The point adjustment (required).
EXPLANATION	A short message describing the reason for the adjustment (required).
keepers	Import an owner's keeper selections. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
KEEP	Comma-separated list of player IDs to keep. Players not listed here and previously marked as kept will be removed from the players to keep. Passing an empty list will result in all keepers being unselected.
FRANCHISE_ID	The franchise to which this keepers list applies. Only valid when the request comes from the league commissioner.
myWatchList	Update the players in an owner's My Watch List. Test it!
Access restricted to league owners.	
L	League Id(required)
ADD	Comma-separated list of player IDs to add to the list.
REMOVE	Comma-separated list of player IDs to remove from the list.
Other League Info
accounting	Import one accounting record. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
FRANCHISE	The franchise id of the new record (required).
AMOUNT	The amount being credited or debited. Positive values will increase the franchise's balance, while negative values will subtract from it (required).
DESCRIPTION	A short message describing the transaction (required).
franchiseScoreAdjustment	Import one franchise score adjustment record. Test it!
Access Restricted: Requires cookie from league commissioner.	
L	League Id(required)
FRANCHISE	The franchise id whose score is being adjusted (required).
WEEK	The week number of the score being adjusted (required).
POINTS	The point adjustment (required).
EXPLANATION	A short message describing the reason for the adjustment (required).
poolPicks	Make an NFL or Fantasy pool pick. To send the actual picks you need to pass a number of PICK and RANK parameters. These have names like PICKDAL,NYG and RANKDAL,NYG. For example, in a game where the Cowboys are playing at the Giants, and you want to indicate with a confidence level of 16 that the Giants will win, the two name/value pairs that must be passed in are PICKDAL,NYG=NYG and RANKDAL,NYG=16. This needs to be repeated for all matchups (fantasy or NFL) for the week in question. For fantasy pool picks, the franchise ids are used instead of NFL team abbreviations.
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
POOLTYPE	Pool type of picks being imported. Must be either "NFL" or "Fantasy". If not specified, the default is "NFL".
PICK	See description (required).
RANK	See description (required).
WEEK	The week being submitted (only for the commissioner).
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.
survivorPoolPick	Import a survivor pool pick. Test it!
Access restricted to league owners.
Commissioner can impersonate owner using FRANCHISE_ID paramter.	
L	League Id(required)
PICK	Survivor pick, the MyFantasyLeague.com 3-letter NFL abbreviation (required).
FRANCHISE_ID	When called by the Commissioner, you must pass this parameter to indicate on which franchise behalf to do the request.

MyFantasyLeague.com Developers Program API Reference
Before you attempt to use this API, you should first read the General Information Page. You can also view sample code or test the requests.

Show requests for command: Misc  

Unless otherwise specified, these requests are invoked using the format protocol://host/year/request?args

Misc Requests
Request	Description	Arguments
Draft & Auction
live_draft	Attempts to draft the specified player or otherwise control the draft timer. When making a pick, the round and pick parameters (and the franchise id if required) must match the current "On The Clock" info. They can not be used to change earlier or later picks. For more info, check this FAQ. Test it!
Access restricted to league owners.	
L	League Id(required)
CMD	This needs to be set to "DRAFT" to make a draft pick. It may also be set to "PAUSE" or "RESUME" which pauses or resumes the draft or "SKIP" or "UNDO" which skips the current pick or undoes the last pick. All commands other than "DRAFT" are available to the commissioner only.
PLAYER_PICK	The player id of the player to draft (required for CMD=DRAFT).
FRANCHISE_PICK	When making a draft pick, it will use the franchise id of the user submitting the request. If the request is sent by the commissioner, then this parameter is required to specify the franchise id making the pick.
ROUND	The draft round number for the draft pick (required for CMD=DRAFT).
PICK	The pick number within the current round for the draft pick (required for CMD=DRAFT)
COMMENTS	Optional comments to be included with the pick. Meant for email drafts but accepted for live drafts too. Comments will not be shown in real time on the live draft room.
JSON	If set to 1, the result is returned in JSON format.
XML	If set to 1, the result is returned in XML format.
Communications
chat_save	Posts a message to the league chat. Note that there's no API to get the chat messages. Those are available via the XML file located at http://HOST.myfantasyleague.com/fflnetdynamicYEAR/LEAGUE_ID_chat.xml. You must replace HOST, YEAR and LEAGUE_ID with the proper values. This file might not exist if no chat messages have been written to your league, so your client needs to handle a 404 (page not found) response. Test it!
Access restricted to league owners.	
L	League Id(required)
MESSAGE	A URL-encoded string that will be posted to the league chat. (Spaces are represented by + signs, all other non-alphanumeric characters should be encoded as %. (required)
TO_FID	Optional argument that if specified makes the message only visible to the given franchise id.
User Functions
login	Validates a set of user credentials (username/password) and if valid, returns the cookie for that user. Note that this request should be sent via HTTPS and using the POST method to avoid exposing the user credentials on insecure networks. Support for this API via GET may be discontinued at any point. More info is available here. Test it!	
USERNAME	The login username (required).
PASSWORD	The login password (required).
XML	This needs to be set to 1 to avoid returning a full HTML page (required).
NFL Content
mfl_status	Returns the current week and other related variables. Use this in order to tell what is the current week. To access this use https://api.myfantasyleague.com/fflnetdynamic2026/mfl_status.xml. A JSON option is also available (use .json instead of .xml.)	 
nfl_sched	Returns the full NFL schedule. Data for in-progress games updates every 15 minutes. To access this use: https://api.myfantasyleague.com/fflnetdynamic2026/nfl_sched.xml. A JSON option is also available (use .json instead of .xml.)	 
nfl_sched_X	Returns the NFL schedule for week X. Data for in-progress games updates every 15 minutes. To access this for week 1 use: https://api.myfantasyleague.com/fflnetdynamic2026/nfl_sched_1.xml. Replace the '1' with a different week number to get the appropriate week schedule. A JSON option is also available (use .json instead of .xml.)	 
