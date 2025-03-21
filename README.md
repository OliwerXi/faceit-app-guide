# Guide for my FACEIT application features
> [!IMPORTANT]
> The features here are <span style="font-weight: 500;color: #E97451;">purchase-based</span> and not implemented by default.

### Twitch Commands <code style="margin-left: 5px;color: #E97451;">twitch-commands.json</code>
<a name="twitch-commands"></a>
***
Commands that are executable through your Twitch chat with whatever account you linked as the sender.
The setup is fairly simple and is just a [<span style="color: #E97451 !important;">JSON</span>](https://en.wikipedia.org/wiki/JSON "Wikipedia page for JSON") file. Feel free to check out the [<span style="color: #E97451 !important;">example</span>](./twitch-commands.json "Example Twitch Commands file") before moving on.

The way it is structured is using the [<span style="color: #E97451 !important;">JSON</span>](https://en.wikipedia.org/wiki/JSON "Wikipedia page for JSON") syntax and requires an [<span style="color: #E97451 !important;">object</span>](https://www.digitalocean.com/community/tutorials/an-introduction-to-json#understanding-syntax-and-structure "Helpful syntax guide and/or introduction to JSON") mapped by other [<span style="color: #E97451 !important;">objects</span>](https://www.digitalocean.com/community/tutorials/an-introduction-to-json#understanding-syntax-and-structure "Helpful syntax guide and/or introduction to JSON") that represent your commands, where the  with a <code style="margin-left: 5px;color: #E97451;">response</code> property that represents the [<span style="color: #E97451 !important;">string</span>](#json-sample "Sample JSON data with string example") (text) to be sent to the chat as a reply to whenever a command is invoked.

Using the aforementioned information, this is the outcome:
```json
{
  "!my-command": {
    "response": "this is my first command!"
  }
}
```
These commands allow the use of [<span style="color: #E97451 !important;">placeholders</span>](#placeholders) in each and every response.

### Triggers <code style="margin-left: 5px;color: #E97451;">triggers.conf</code>
<a name="triggers"></a>
***
Triggers are actions that are executed when a FACEIT event related to your player occurs.
Similar to the [<span style="color: #E97451 !important;">Twitch Commands</span>](#twitch-commands), the structure here is the same, using [<span style="color: #E97451 !important;">JSON</span>](https://en.wikipedia.org/wiki/JSON "Wikipedia page for JSON"). The notable difference is that to define triggers, each event definition is an [<span style="color: rgb(37, 198, 230);">array</span>](#json-sample) and has [<span style="color: rgb(37, 198, 230);">objects</span>](#json-sample) as its elements with two required string properties, <code style="color: #E97451;">type</code> and <code style="color: #E97451;">input</code>.

The available event values are:

- <code style="color:rgb(233, 81, 170);">MATCH-READY</code> - triggered when a new match is found.
- <code style="color:rgb(233, 81, 170);">MATCH-STARTED</code> - triggered when all players have connected and it starts.
- <code style="color:rgb(233, 81, 170);">MATCH-CANCELLED</code> - triggered when the match was cancelled for reasons such as a player failed to connect during warmup.
- <code style="color:rgb(233, 81, 170);">MATCH-FINISHED</code> - triggered when the match has finished.

and possible <code style="color: #E97451;">type</code> values:

- <code style="color:rgb(233, 81, 170);">SEND_TWITCH_MESSAGE</code> - send a message in the Twitch chat as your configured user (if any, otherwise ignored).
- <code style="color:rgb(233, 81, 170);">UPDATE_TWITCH_TITLE</code> - update the title on your stream as your configured user (if any, otherwise ignored).

> [!IMPORTANT]
> Don't re-define the event keys or else it will use the last one defined and ignore previous.  
> That said, each array is a list of triggers for every event.

Using the aforementioned information here as well, an outcome similar to this is inevitable:
```json
{
  // triggers for when a match is found
  "MATCH-READY": [
    {
      // sends 'A match was found: https://www.faceit.com/en/cs2/room/ID_HERE'
      "type": "SEND_TWITCH_MESSAGE",
      "input": "A match was found: https://www.faceit.com/en/cs2/room/{faceit:current:id}"
    }
  ],
  // triggers for when the match starts
  "MATCH-STARTED": [
    {
      // sends 'All players have connected and the match is starting.'
      "type": "SEND_TWITCH_MESSAGE",
      "input": "All players have connected and the match is starting."
    }
  ],
  // triggers for when the match finishes
  "MATCH-FINISHED": [
    {
      // sends 'The match has completed as our new Elo is 1,000.'
      "type": "SEND_TWITCH_MESSAGE",
      "input": "The match has completed as our new Elo is {faceit:profile:elo}."
    },
    {
      // updates title to 'TOP #1 FACEIT PLAYER'
      "type": "UPDATE_TWITCH_TITLE",
      "input": "TOP #{faceit:profile:regionRank} FACEIT PLAYER"
    }
  ]
}
```
The inputs in these triggers allow the use of [<span style="color: #E97451 !important;">placeholders</span>](#placeholders).

### Placeholders
<a name="placeholders"></a>
***

> [!IMPORTANT]
> Replacements in any and all placeholder parameters:  
> <code>&amp;#44;</code> always gets replaced with <code>,</code>.

<p style="font-weight: 500;color: #E97451;">Player Profile</p>

- <code>{faceit:profile:elo}</code> - current Elo.
- <code>{faceit:profile:regionRank}</code> - current regional placement.
- <code>{faceit:profile:countryRank}</code> - current country placement.

<p style="font-weight: 500;color: #E97451;">Current Match Data</p>

- <code>{faceit:current:id}</code> - unique identifier of the current match room, empty if none.
- <code>{faceit:current:[T]:*}</code> - data related to each team (<code style="color: #E97451;">[T]</code> can be either <code style="color: #E97451;">self</code> or <code style="color: #E97451;">opponent</code>)
  - <code>{faceit:current:[T]:name}</code> - name of the respective team (e.g. <code style="color: #E97451;">team_SomeNameHere</code>)
  - <code>{faceit:current:[T]:eloGain}</code> - estimated Elo gain of the respective team.
  - <code>{faceit:current:[T]:eloLoss}</code> - estimated Elo loss of the respective team.
  - <code>{faceit:current:[T]:avgElo}</code> - the average Elo of the respective team.

<p style="font-weight: 500;color: #E97451;">Seasonal Data - <code style="color: rgb(37, 198, 230);">S</code> can be between <code style="color: rgb(37, 198, 230);">1-4</code> or <code style="color: rgb(37, 198, 230);">alltime</code></p>

- <code>{faceit:seasonal(S):stats:matches}</code> - total matches played.
- <code>{faceit:seasonal(S):stats:wins}</code> - count of matches won.
- <code>{faceit:seasonal(S):stats:losses}</code> - count of matches lost.
- <code>{faceit:seasonal(S):stats:rounds}</code> - total rounds played.
- <code>{faceit:seasonal(S):stats:kills}</code> - kill count.
- <code>{faceit:seasonal(S):stats:deaths}</code> - death count.
- <code>{faceit:seasonal(S):stats:assists}</code> - assist count.
- <code>{faceit:seasonal(S):stats:headshots}</code> - headshot count.
- <code>{faceit:seasonal(S):stats:mvps}</code> - total MVPs through all matches.
<div></div>

- <code>{faceit:seasonal(S):[R]:*}</code> - highest and lowest data during the specified season (<code style="color: #E97451;">[R]</code> can be either <code style="color: #E97451;">highest</code> or <code style="color: #E97451;">lowest</code>)
  - <code>{faceit:seasonal(S):[R]:elo}</code> - the Elo reached in the specified range.
  - <code>{faceit:seasonal(S):[R]:map}</code> - the map that the Elo was reached on.
  - <code>{faceit:seasonal(S):[R]:on}</code> - the date and time of when it was reached, formatted as [<span style="color: #E97451 !important;">ISO 8601</span>](https://sv.wikipedia.org/wiki/ISO_8601 "Wikipedia reference for the ISO 8601 standard") UTC.

Extensive Statistics (***italic*** means OPTIONAL)

- <p style="font-weight: 500;color:rgb(180, 94, 230);">Possible tags (<code style="color:rgb(233, 81, 187);">?</code> is one of <code style="color:rgb(233, 81, 187);">limit</code>, <code style="color:rgb(233, 81, 187);">dated</code> or <code style="color:rgb(233, 81, 187);">season</code> as shown below):</p>

  - <code>{faceit:stats:?:matches}</code> - total matches.
  - <code>{faceit:stats:?:wins}</code> - total wins.
  - <code>{faceit:stats:?:losses}</code> - total losses.
  - <code>{faceit:stats:?:rounds}</code> - total rounds.
  - <code>{faceit:stats:?:eloDiff}</code> - the elo difference.
  - <code>{faceit:stats:?:eloDiffStr}</code> - the elo difference formatted as string with operator signs.
  - <code>{faceit:stats:?:hsRate}</code> - the total headshot rate.
  - <code>{faceit:stats:?:winRate}</code> - the win-rate.
  - <code>{faceit:stats:?:kills}</code> - total kills.
  - <code>{faceit:stats:?:deaths}</code> - total deaths.
  - <code>{faceit:stats:?:headshots}</code> - total headshots.
  - <code>{faceit:stats:?:enemiesFlashed}</code> - total enemies flashed.
  - <code>{faceit:stats:?:damage}</code> - total damage.
  - <code>{faceit:stats:?:utilDamage}</code> - total utility damage.
  - <code>{faceit:stats:?:avg:}</code> - average kills.
  - <code>{faceit:stats:?:avg:deaths}</code> - average deaths.
  - <code>{faceit:stats:?:avg:kdr}</code> - average K/D.
  - <code>{faceit:stats:?:avg:kpr}</code> - average kill per round.
  - <code>{faceit:stats:?:avg:adr}</code> - average ADR.
  - <code>{faceit:stats:?:avg:hsRate}</code> - average headshot rate.
  - <code>{faceit:stats:?:avg:enemiesFlashed}</code> - average enemies flashed.
  - <code>{faceit:stats:?:avg:utilDamage}</code> - average utility damage.

<div></div>

- <code>{faceit:stats:limit(count,offset):*}</code> - statistics of N matches decided by the passed <code style="color: #E97451;">count</code> parameter (<code style="color: #E97451;">-1</code> equals to ALL matches). ***offset*** is the starting point of the matches, for example start from <code style="color: #E97451;">5</code> and then <code style="color: #E97451;">20</code> for <code style="color: #E97451;">count</code> would select the 20 matches **AFTER** the 5th.

- <code>{faceit:stats:dated(from,to,limit):*}</code> - statistics of matches between the date(s) passed, <code style="color: #E97451;">from</code> being the oldest date to start FROM, ***to*** being the newest date and then ***limit*** being the potential limitation of matches collected for statistical analysis. 

  > Please note that the dates have to be either in unix milliseconds, valid date format, or a mix of <code style="color: #E97451;">date_here|+1d</code> or <code style="color: #E97451;">date_here|-1d</code> which adds/removes a day to/from the date specified, respectively. For mixed (addition and reduction) dates, the valid operators are <code style="color: #E97451;">+</code> and <code style="color: #E97451;">-</code>.  
  >  
  > The possible time units are:  
  > <code style="color: #E97451;">s</code> for seconds,
  > <code style="color: #E97451;">m</code> for minutes,
  > <code style="color: #E97451;">h</code> for hours,
  > <code style="color: #E97451;">d</code> for days,  
  > <code style="color: #E97451;">w</code> for weeks,
  > <code style="color: #E97451;">y</code> for years and
  > <code style="color: #E97451;">mo</code> for months.
  >  
  > There are also helper definitions for dates:  
  > <code style="color: #E97451;">MIDNIGHT</code> - this helper definition defines the date today as of midnight 00:00 UTC.

- <code>{faceit:stats:season(S):*}</code> - statistics of a season where <code style="color: rgb(37, 198, 230);">S</code> is a season between <code style="color: rgb(37, 198, 230);">1-4</code>.

<p style="font-weight: 500;color: #E97451;">Season Dates - <code style="color: rgb(37, 198, 230);">S</code> can be between <code style="color: rgb(37, 198, 230);">1-4</code> and are formatted as <code>1st January 1970</code></p>

  - <code>{faceit:season(S):start}</code> - the starting date of the respective season.
  - <code>{faceit:season(S):end}</code> - the ending date of the respective season.

<p style="font-weight: 500;color: #E97451;">Conditional Values - <code style="color: rgb(37, 198, 230);">C</code> can only be <code style="color: rgb(37, 198, 230);">IS_IN_FACEIT_MATCH</code> currently</p>

  - <code>{condition(C,hello world)}</code> - if <code style="color: rgb(37, 198, 230);">C</code> is true, `hello world` is displayed, otherwise an empty string/text since no `false` parameter was specified.
  - <code>{condition(C,hello world,world hello)}</code> - if <code style="color: rgb(37, 198, 230);">C</code> is false, `world hello` is displayed.

### Sample object using [<span style="color: #E97451 !important;">JSON</span>](https://en.wikipedia.org/wiki/JSON "Wikipedia page for JSON")
<a name="json-sample"></a>
***
> **value**: any literal from object to array, to boolean, null, number and strings.  
> **object**: representation of a key-value pair (<code style="color: #E97451;">{}</code> are the delimiters).  
> **array**: representation of a list containing zero, one or more values (<code style="color: #E97451;">[]</code> are the delimiters).  
>  
> Note that after each item in an object/array, there needs to be a comma to separate between the current and next value, as displayed below.  
> *sample string*, *sample object* and *a nested property* are all keys with their respective values, objects and/or arrays.

```json
// this is also an object (the top-level if you will; can also be changed to an array
//                         with [] delimiters if the situation calls for it)
{
  "sample string": "this is a string value",
  "sample object": {
    "a nested property": 1, // this is a number value
    "another nested property": null // `null` represents the absence of a value
  },
  "sample array": [
    // string literal
    "hello world",
    // number literal
    1,
    // no value (or absence of one)
    null,
    // object literal
    {
      "object property": "with a string literal"
    },
    // array literal
    [
      "string literal in an array, nested inside another array"
    ]
  ]
}
```

### Extra Notes
***
A simpler web-view for [<span style="color: #E97451 !important;">Twitch Commands</span>](#twitch-commands) and [<span style="color: #E97451 !important;">Triggers</span>](#triggers) will be in the works soon.
