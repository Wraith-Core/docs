# w_lib — Unified Library

**Repository:** [`Wraith-Core/w_lib`](https://github.com/Wraith-Core/w_lib)  
**Dependency:** `oxmysql`, FXServer `14901+`, OneSync

`w_lib` is the shared foundation loaded by every Wraith Core resource. It provides a single unified NUI frame (Red Dead paper-and-ink theme), typed Lua 5.4 modules, callbacks, world prompts, object placer, particle glow markers, database helpers, localization, and framework/inventory bridges.

---

## Loading `w_lib`

Add `@w_lib/init.lua` as the first shared script in `fxmanifest.lua`:

```lua
lua54 'yes'
dependency 'w_lib'

shared_scripts {
    '@w_lib/init.lua',
}
```

Once loaded, two identical global tables are available on both client and server: **`w`** and **`lib`** (`w == lib`), plus **`cache`** on the client (`cache.ped`, `cache.playerId`, `cache.coords`, `cache.vehicle`, `cache.seat`, `cache.mount`).

---

## Callbacks (`w.callback`)

### Server Registration

```lua
w.callback.register('my_script:getBalance', function(source, accountId)
    -- return any number of values
    return true, 250
end)
```

### Client Await

```lua
local ok, amount = w.callback.await('my_script:getBalance', false, 'savings')
```

### Client Callback (Server → Client)

```lua
-- Client
w.callback.register('my_script:confirmAction', function(title)
    return w.alertDialog({ header = title, content = 'Continue?', cancel = true }) == 'confirm'
end)

-- Server
local accepted = w.callback.await('my_script:confirmAction', source, 'Confirm Trade')
```

---

## Interface & Notifications (Client)

### Notifications (`w.notify`)

```lua
w.notify({
    title = 'Bank',
    description = 'Deposited $150.00 into your account.',
    type = 'success', -- 'inform' | 'success' | 'warning' | 'error'
    duration = 4000,
})
```

### Context Menus

```lua
w.registerContext({
    id = 'my_menu',
    title = 'General Services',
    options = {
        {
            title = 'Open Storage',
            description = 'Access your personal locker',
            onSelect = function()
                exports.w_inventory:openInventory('stash', 'my_locker')
            end,
        },
    },
})

w.showContext('my_menu')
```

### Dialogs & Input

```lua
-- Confirmation alert
local res = w.alertDialog({
    header = 'Confirm Sale',
    content = 'Sell this item for $25?',
    centered = true,
    cancel = true,
})

-- Input form
local input = w.inputDialog('Transfer Funds', {
    { type = 'number', label = 'Citizen ID', required = true },
    { type = 'number', label = 'Amount ($)', min = 1, required = true },
})
```

### Text UI & Progress Bars

```lua
w.showTextUI('[J] Open Shop')
w.hideTextUI()

local completed = w.progressBar({
    duration = 5000,
    label = 'Bandaging wounds...',
    useWhileDead = false,
    canCancel = true,
    disable = { move = true, combat = true },
})
```

---

## World Prompts (`w.prompt`)

Wraith Core standardizes world interaction on the **`J`** key (never `E`, which grabs/chokes nearby players in RDR2).

```lua
-- Walk-up coordinate prompt
local handle = w.prompt.at(vec3(-322.4, 803.8, 117.9), {
    key = 'J',
    label = 'Open General Store',
    distance = 2.5,
    onComplete = function()
        -- triggered when pressed/held
    end,
})

-- Entity prompt
w.prompt.atEntity(pedHandle, {
    key = 'J',
    label = 'Talk to Clerk',
    distance = 3.0,
    onComplete = function() end,
})

-- Multi-key prompt group
local group = w.prompt.createGroup('Campfire')
```

---

## Particle Glow Markers (`w.glow`)

Prefer `w.glow` over `DrawMarker` for RedM world highlights:

- **Kinds:** `1` = trail (line), `2` = clue (point), `3` = entity (arrow), `4` = lootable_plant (circle), `5` = pickup highlight (wall).
- Supports per-frame drawing (`w.glow.frame(key, kind, ...)`) and solid top-down visibility (`opts.solid = true`).
- Controlled by `setr w_lib:glowBudget 150`.
- Test styles in-game with `/glow`.

---

## Blips, Navigation & Placer

```lua
-- Create a map blip
local blip = w.blip.create(vec3(-322.4, 803.8, 117.9), 'General Store', 'blip_shop_store')

-- Set GPS waypoint
w.navigation.setWaypoint(-322.4, 803.8)

-- Interactive 3D object / coordinate placer (uses standardized w_lib controls)
-- Available under w.placer
```

---

## Database Exports (Server)

`w_lib` wraps `oxmysql` with consistent server exports:

```lua
local rows   = exports.w_lib:DbQuery('SELECT * FROM players WHERE license = ?', { license })
local row    = exports.w_lib:DbSingle('SELECT citizenid FROM players WHERE id = ?', { id })
local count  = exports.w_lib:DbScalar('SELECT COUNT(*) FROM players', {})
local newId  = exports.w_lib:DbInsert('INSERT INTO my_table (name) VALUES (?)', { 'Valentine' })
local aff    = exports.w_lib:DbUpdate('UPDATE my_table SET name = ? WHERE id = ?', { 'Rhodes', newId })
local ok     = exports.w_lib:DbTransaction({
    { query = 'UPDATE my_table SET a = 1 WHERE id = ?', values = { 1 } },
    { query = 'UPDATE my_table SET a = 2 WHERE id = ?', values = { 2 } },
})
```

> **Tip:** `DbTransaction` can return `true` even if a guarded `UPDATE` matched `0` rows. Always re-read version/affected counts when doing optimistic locking.

---

## Framework & Inventory Bridge (`w.framework`)

Resources built for Wraith Core (and portable across frameworks) can use `w_lib`'s bridge exports:

- **Framework:** `Fw*` exports (`FwGetPlayer`, `FwGetPlayerData`, `FwHasPermission`, etc.)
- **Inventory:** `Inv*` exports (`InvAddItem`, `InvRemoveItem`, `InvGetItemCount`, etc.)
- **Citizen Resolution:** `CitizenFromSource`, `CitizenIdOf`, `CitizenResolve`, `CitizenRequireOnline`
- **Doorlock Bridge:** `DoorGetState`, `DoorSetState`, `DoorGetClosest`, `DoorOnStateChanged`

---

## Localization (`locale`)

Declare `w_lib { 'locale' }` in `fxmanifest.lua` and place JSON files in `locales/en.json` and `locales/ar.json`.

```lua
local text = locale('welcome_message', playerName)
local lang = w.getLocaleKey() -- 'en' or 'ar'

-- React to live client language changes
AddEventHandler('w_lib:setLocale', function(newLang)
    w.locale(newLang)
end)
```

---

## Quiet Console & Banners

`w_lib` keeps the server console clean:
- Prints **one English startup banner** per resource (`name`, `version`, `description`).
- Routine `print()` lines are suppressed unless they contain an error/warning marker (`^1`, `error`, `fail`, `warn`, `blocked`, `exception`, `traceback`).
- Enable full debug printing when needed:
  ```cfg
  setr w_lib:consoleDebug 1
  # or for a single resource:
  setr my_resource:consoleDebug 1
  ```

---

## Full Reference

For exhaustive module documentation (16,000+ lines covering every helper, UI option, math/table utility, and bridge export), see [`w_lib/README.md`](https://github.com/Wraith-Core/w_lib/blob/main/README.md).
