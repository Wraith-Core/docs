# Interface & World Resources

---

## `w_adminmenu`

**Repository:** [`Wraith-Core/w_adminmenu`](https://github.com/Wraith-Core/w_adminmenu)  
**License:** MIT  
**Dependencies:** `w_lib`, `w_core`

Open-source RedM Admin Menu built 100% with **`w_lib`** native interfaces (`lib.registerContext`, `lib.inputDialog`, `lib.alertDialog`, `lib.notify`, `lib.showTextUI`, `lib.setClipboard`).

- **Open:** Press **`F10`** (default keybind) or type **`/admin`**.
- **Access:** Requires `w_adminmenu.open` ACE (granted to `group.admin` / `group.god` in `permissions.cfg`).
- **Tools:** Noclip, God Mode, Invisibility, Self Heal/Revive, Waypoint/Coords/Town Teleports, Horse & Wagon Spawner, Online Player Management (Heal, GoTo, Bring, Spectate, Freeze, Inventory, Give Item, Money, Job/Gang, Kick, Ban), Server Announcements, Weather/Time Controls, Active Bans/Unban, and Developer Coords Copy.

---

## `w_hud`

**Repository:** [`Wraith-Core/w_hud`](https://github.com/Wraith-Core/w_hud)  
**Dependencies:** `w_lib`, `oxmysql`

Player HUD, server-authoritative needs (hunger & thirst), satchel weight bonus, voice proximity indicator, and server-wide announcements.

### Server & Client Exports

```lua
-- Server-wide announcement banner (top-center under compass)
exports.w_hud:Announce('Server restart in 5 minutes.', 15, {
    title = 'Announcement',
    target = -1, -- -1 = everyone, or specific player source
})

-- Needs management (Server)
exports.w_hud:AddNeed(source, 'hunger', 25)
exports.w_hud:AddNeeds(source, { hunger = 20, thirst = 30 })
exports.w_hud:SetNeed(source, 'thirst', 100)
local needs = exports.w_hud:GetNeeds(source)
exports.w_hud:ResetNeeds(source)

-- HUD visibility & settings (Client)
exports.w_hud:SetDisplay(true)
exports.w_hud:OpenHudSettings()
exports.w_hud:SetVoiceLevel(2)
```

### Commands
- `/hudsettings` — Open per-player HUD customization panel.

---

## `w_chat`

**Repository:** [`Wraith-Core/w_chat`](https://github.com/Wraith-Core/w_chat)  
**Dependencies:** `w_lib`

Clean, roleplay-ready chat styled to match `w_lib`, with command autocomplete suggestions and `/clear`.

```lua
-- Add a message via export
exports.w_chat:addMessage({
    args = { 'System', 'Welcome to the server!' },
})
```

---

## `w_loading`

**Repository:** [`Wraith-Core/w_loading`](https://github.com/Wraith-Core/w_loading)  
**Dependencies:** `w_lib`

Red Dead survey-plate loading screen with period music, tips, honest progress bar, and a seamless transition curtain used between character selection, creation, and spawn.

### Configuration (`server.cfg` or `config.lua`)

```cfg
setr w_loading:serverName "My RedM Server"
setr w_loading:brandLatin "MY SERVER"
setr w_loading:brandArabic "MY SERVER"
```

### Client Exports

```lua
exports.w_loading:Show('spawn') -- raise the transition curtain
exports.w_loading:Hide()        -- lower the curtain
exports.w_loading:Dismiss()     -- fade out and shut down the initial loading screen
local shown = exports.w_loading:IsShown()
```

---

## `w_zones`

**Repository:** [`Wraith-Core/w_zones`](https://github.com/Wraith-Core/w_zones)  
**License:** MIT  
**Dependencies:** None (standalone)

Free **100% native RDR2** zone announcement banner using `_UI_FEED_POST_LOCATION_SHARD` (`0xD05590C1AB38F068`) with **zero NUI**.

- Automatically announces Towns, Districts, and States as players travel.
- Shows in-game clock (12h/24h) and local temperature (°C/°F) according to each player's game settings.
- `/zone` command re-displays the current location banner.

```lua
local zone = exports.w_zones:GetCurrentZone() -- { town, district, state }
exports.w_zones:ShowCurrentZone()
exports.w_zones:ShowLocationBanner('The Heartlands, NH · 14:30 · 22°C', 'Valentine', 4000)
```

---

## `w_weathersync`

**Repository:** [`Wraith-Core/w_weathersync`](https://github.com/Wraith-Core/w_weathersync)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Server-synced weather, time-of-day, dynamic weather transitions, time freeze, and blackout states.

### Exports

```lua
exports.w_weathersync:SetWeather('RAIN')
exports.w_weathersync:SetTime(12, 0)
exports.w_weathersync:setDynamicWeather(true)
exports.w_weathersync:setTimeFreeze(false)
exports.w_weathersync:setBlackout(false)

local weather = exports.w_weathersync:GetWeather()
local time    = exports.w_weathersync:GetTime()
```

---

## `w_interiors`

**Repository:** [`Wraith-Core/w_interiors`](https://github.com/Wraith-Core/w_interiors)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Automatically activates interior entity sets and IPLs/IMAPs across the RedM map so towns, saloons, offices, and interiors load properly without missing walls or props.

---

## `w_doorlock`

**Repository:** [`Wraith-Core/w_doorlock`](https://github.com/Wraith-Core/w_doorlock)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Full-featured door locking system with job/gang/item permissions, lockpicking support, database persistence (`w_doorlock` table), and an in-game visual editor (`/doorlock`).

### Exports

```lua
-- Set lock state (1 = locked, 0 = unlocked)
exports.w_doorlock:setDoorState(doorId, 1)

-- Query doors
local door    = exports.w_doorlock:getDoor(doorId)
local byName  = exports.w_doorlock:getDoorFromName('valentine_sheriff_cell_1')
local closest = exports.w_doorlock:getClosestDoor()
local all     = exports.w_doorlock:getAllDoors()
```

*(Also accessible via `w_lib` door bridge exports: `exports.w_lib:DoorGetState`, `DoorSetState`, `DoorGetClosest`.)*

---

## `w_fasttravel`

**Repository:** [`Wraith-Core/w_fasttravel`](https://github.com/Wraith-Core/w_fasttravel)  
**License:** MIT  
**Dependencies:** `w_lib`, `w_core`

Fast travel & stagecoach station network across 10 RDR2 towns (Valentine, Rhodes, Saint Denis, Blackwater, Strawberry, Emerald Ranch, Annesburg, Van Horn, Armadillo, Tumbleweed), built 100% with **`w_lib`** (`w.prompt.at`, `w.blip`, `lib.registerContext`, `lib.alertDialog`, `lib.progressBar`, `lib.notify`).

- **Interaction:** Walk up to any Fast Travel post and press **`J`**.
- **Pricing:** Supports fixed per-town prices or distance-based pricing (`Config.PricingMode = 'fixed' | 'distance'`).
- **Security:** Server verifies departure proximity, player state, cooldown, and charges `cash` (or `bank`) before travel.
