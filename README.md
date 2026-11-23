# SquadJS Activity Tracker

A SquadJS plugin that tracks player activity to help server moderators identify AFK players. It monitors in-game events such as kills, deaths, damage, revives, squad actions, etc., and then allows moderators to check for latest activity using chat commands.

## Usage

Type these commands in the in-game admin chat (or any chat if `adminChatOnly` is `false`):

| Command | Description |
|---|---|
| `!afk` | List all players inactive longer than the default threshold (default: 10 min) |
| `!afk 30` | List all players inactive longer than 30 minutes |
| `!afk PlayerName` | Show a specific player's inactivity duration and last activity type |
| `!afk sq 2` | List all members of Squad 2 on your team with their inactivity times |
| `!afk other sq 2` | List all members of Squad 2 on the enemy team with their inactivity times |

Only players assigned to a squad are included in the list output. Unassigned players are excluded (handled by other plugins like auto-kick-unassigned).

### Example Output

```
--- AFK Report --- 3 player(s) >10m
[42m] T:1/S:3 PlayerName1 (last: Kill)
[35m] T:2/S:1 PlayerName2 (last: Connected)
[12m] T:1/S:5 PlayerName3 (last: Revive)
```

The list is sorted by longest inactivity and limited to `maxPage` warn messages. If more players match, the last message ends with `... +N more, try !afk <higher minutes>`.

## Tracked Events

The plugin records player activity from the following server events:

| Event | Activity Recorded For | Activity Label |
|---|---|---|
| `PLAYER_WOUNDED` | Attacker + Victim | `Wound` / `Wounded` |
| `PLAYER_DIED` | Victim + Attacker | `Death` / `Kill` |
| `PLAYER_REVIVED` | Reviver + Revived player | `Revive` / `Revived` |
| `PLAYER_SQUAD_CHANGE` | Player | `SquadChange` |
| `CHAT_MESSAGE` | Player (optional, can be disabled) | `Chat` |
| `SQUAD_CREATED` | Player | `SquadCreated` |
| `PLAYER_TEAM_CHANGE` | Player | `TeamChange` |
| `TEAMKILL` | Attacker + Victim | `Teamkill` / `Teamkilled` |
| `PLAYER_DAMAGED` | Attacker + Victim | `Damage` / `Damaged` |
| `DEPLOYABLE_DAMAGED` | Attacker | `DeployableDmg` |
| `PLAYER_CONNECTED` | Player (initializes tracking) | `Connected` |
| `PLAYER_DISCONNECTED` | Player (removes tracking data) | — |
| `NEW_GAME` | Clears all data and re-seeds connected players | `NewGame` |

Players already connected when the plugin starts are seeded with the label `Mount`. Players without any record show `Unknown`.

## Configuration

Add the plugin to your SquadJS `config.json` under `plugins`:

```json
{
  "plugin": "ActivityTracker",
  "enabled": true,
  "defaultInactiveMinutes": 10,
  "ignoreChatForActivity": false,
  "adminChatOnly": true,
  "commandPrefix": "afk",
  "warnMessageHeader": "--- AFK Report ---",
  "maxPage": 2
}
```

### Options

| Option | Type | Default | Description |
|---|---|---|---|
| `defaultInactiveMinutes` | Number | `10` | Inactivity threshold in minutes when running `!afk` with no argument |
| `ignoreChatForActivity` | Boolean | `false` | If `true`, chat messages will **not** count as player activity |
| `adminChatOnly` | Boolean | `true` | If `true`, the `!afk` command only works in admin chat (`ChatAdmin`) |
| `commandPrefix` | String | `"afk"` | The chat command trigger word (without the `!` prefix) |
| `warnMessageHeader` | String | `"--- AFK Report ---"` | Header text shown at the top of AFK report messages |
| `maxPage` | Number | `2` | Maximum number of warn pages (messages) to send per command response |

All options are optional and have sensible defaults. No connectors (Discord, database) are required.

## Installation

Copy `activity-tracker.js` into your SquadJS `squad-server/plugins/` directory and add the configuration block above to your `config.json`.

## License

[GPL-3.0](LICENSE)
