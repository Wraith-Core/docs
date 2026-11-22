# w_core — Framework Core

**Repository:** [`Wraith-Core/w_core`](https://github.com/Wraith-Core/w_core)  
**License:** GPL-3.0  
**Dependencies:** `oxmysql`, `w_lib`

`w_core` is the central framework layer managing characters (`players`), money accounts, jobs, gangs, player metadata, database-backed staff ranks/ACE permissions, bans, and core server/admin commands.

---

## Accessing the Core Object

Always cache `GetCoreObject()` **once** at resource load — never call it inside loops or per-event handlers.

```lua
-- Server or Client
local WCore = exports.w_core:GetCoreObject()
```

---

## Server API (`WCore.Functions`)

### Player Lookups

```lua
local player  = WCore.Functions.GetPlayer(source)
local byCid   = WCore.Functions.GetPlayerByCitizenId(citizenid)
local offline = WCore.Functions.GetOfflinePlayerByCitizenId(citizenid)
local players = WCore.Functions.GetWPlayers() -- map of [source] = Player
local ids     = WCore.Functions.GetPlayers()  -- list of server IDs

-- Duty helpers
local onDutyList, count = WCore.Functions.GetPlayersOnDuty('police')
local doctorCount       = WCore.Functions.GetDutyCount('doctor')
```

### Routing Buckets & Identifiers

```lua
local license = WCore.Functions.GetIdentifier(source, 'license')
local src     = WCore.Functions.GetSource(license)

WCore.Functions.SetPlayerBucket(source, 0)
WCore.Functions.SetEntityBucket(entity, 0)
```

### Permissions & Bans

```lua
local isAdmin = WCore.Functions.HasPermission(source, 'admin')
WCore.Functions.AddPermission(source, 'mod')
WCore.Functions.RemovePermission(source, 'mod')
WCore.Functions.Kick(source, 'Reason for kick')
```

---

## Player Object (`player.PlayerData` & `player.Functions`)

Every online character has a `Player` table on the server:

### Key Fields (`player.PlayerData`)

| Field | Type | Description |
| :--- | :--- | :--- |
| `source` | `number` | Current server player ID |
| `citizenid` | `string` | Unique character identifier |
| `license` | `string` | Rockstar license identifier |
| `name` | `string` | Player account name |
| `charinfo` | `table` | `firstname`, `lastname`, `birthdate`, `gender`, `nationality`, `account` |
| `money` | `table` | Account balances (`cash`, `bank`, etc.) |
| `job` | `table` | `name`, `label`, `payment`, `onduty`, `isboss`, `grade = { name, level }` |
| `gang` | `table` | `name`, `label`, `isboss`, `grade = { name, level }` |
| `metadata` | `table` | `hunger`, `thirst`, `isdead`, `inlaststand`, `ishandcuffed`, `injail`, `callsign`, etc. |
| `position` | `table` | Last saved world coordinates |

### Money Methods

```lua
local cash = player.Functions.GetMoney('cash')
local ok   = player.Functions.AddMoney('cash', 50, 'mission-reward')
local paid = player.Functions.RemoveMoney('bank', 100, 'horse-purchase')
player.Functions.SetMoney('cash', 25, 'admin-set')
```

### Jobs & Gangs

```lua
player.Functions.SetJob('police', 2)
player.Functions.SetJobDuty(true)
player.Functions.SetGang('odriscolls', 1)
```

### Metadata & Saving

```lua
player.Functions.SetMetaData('isdead', false)
local isDead = player.Functions.GetMetaData('isdead')

player.Functions.Save()
player.Functions.UpdatePlayerData()
```

> **RedM Note:** Server-side `GetEntityHealth(ped)` can report `0` for live peds in RedM. Always check `player.PlayerData.metadata.isdead` (or `exports.w_medic:IsDead`) for authoritative death state.

---

## Client API (`WCore.Functions`)

```lua
local playerData = WCore.Functions.GetPlayerData()
local isLoggedIn = LocalPlayer.state.isLoggedIn

-- Native RDR2 notifications
exports.w_core:ShowLocationNotification('The Heartlands, NH', 'Valentine', 4000)
exports.w_core:ShowTopNotification('Notice', 'Something happened', 4000)
exports.w_core:ShowBasicTopNotification('Welcome back', 4000)
exports.w_core:ShowSimpleCenterText('Loading...', 3000)
```

---

## Core Lifecycle Events

### Client Events
- `WCore:Client:OnPlayerLoaded` — Fired when the player finishes selecting/spawning their character.
- `WCore:Client:OnPlayerUnload` — Fired when logging out to character selection.
- `WCore:Client:OnJobUpdate` — `(job)` Fired when job or grade changes.
- `WCore:Client:OnGangUpdate` — `(gang)` Fired when gang or grade changes.
- `WCore:Client:SetDuty` — `(onDuty)` Fired when toggling duty.
- `WCore:Player:SetPlayerData` — `(playerData)` Fired whenever player data syncs.

### Server Events
- `WCore:Server:PlayerLoaded` — `(player)` Fired when a character finishes loading on the server.
- `WCore:Server:OnPlayerUnload` — `(source)` Fired when a player unloads their character.
- `WCore:Server:OnMoneyChange` — `(source, moneytype, amount, operation, reason)`
- `WCore:Server:OnJobUpdate` — `(source, job)`
- `WCore:Server:OnGangUpdate` — `(source, gang)`

---

## Jobs, Gangs & Shared Registry Exports

Register or read definitions dynamically across resources:

```lua
-- Jobs
exports.w_core:AddJob('farmer', { label = 'Farmer', defaultDuty = true, grades = { ... } })
local job  = exports.w_core:GetJob('doctor')
local jobs = exports.w_core:GetJobs()

-- Gangs
exports.w_core:AddGang('bandits', { label = 'Bandits', grades = { ... } })
local gangs = exports.w_core:GetGangs()
```

> **Note:** The built-in medical job in Wraith Core is **`doctor`** (grades `0`–`5`), not `ambulance`.

---

## Built-In Commands

| Command | Access | Description |
| :--- | :--- | :--- |
| `/id`, `/cid` | Player | Show your server ID or Citizen ID |
| `/job`, `/gang` | Player | Check your current job/gang and grade |
| `/me`, `/do`, `/ooc` | Player | Roleplay and out-of-character chat commands |
| `/tp`, `/tpm` | Admin | Teleport to coordinates or map waypoint |
| `/dv`, `/dh` | Admin | Delete nearby vehicle or horse |
| `/setjob`, `/setgang` | Admin | Assign a player's job or gang and grade |
| `/givemoney`, `/setmoney` | Admin | Add or set a player's money balance |
| `/addpermission`, `/removepermission` | God/Master | Manage player permission groups |
| `/closeserver`, `/openserver` | Master | Lock/unlock server entry |

---

## Database Tables

Defined in `w_core/wcore.sql` (plus auto-created rank/citizen sequence tables):
- `players` — Character records, money, charinfo, job, gang, position, metadata
- `bans` — Active and historical server bans
- `inventories` — Stash persistence table
- `w_ranks`, `w_rank_members` — Database-backed staff ranks & ACE principals (`wrank.<name>`)
