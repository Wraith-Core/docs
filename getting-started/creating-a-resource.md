# Creating a Wraith Core Resource

This guide walks through creating a clean, idiomatic Wraith Core resource from scratch using `w_lib`, `w_core`, and `w_inventory`.

## Recommended Folder Structure

```text
my_resource/
├── fxmanifest.lua
├── config.lua
├── shared/
│   └── contract.lua     # All event, callback, and NUI names in one place
├── server/
│   └── main.lua
├── client/
│   └── main.lua
└── locales/
    ├── en.json
    └── ar.json
```

---

## 1. `fxmanifest.lua`

Always load `@w_lib/init.lua` as the **first** shared script and declare `lua54 'yes'`.

```lua
fx_version 'cerulean'
game 'rdr3'
rdr3_warning 'I acknowledge that this is a prerelease build of RedM, and I am aware my resources *will* become incompatible once RedM ships.'
lua54 'yes'

name 'my_resource'
author 'Your Name'
version '1.0.0'
description 'Example Wraith Core resource'

dependency 'w_lib'

shared_scripts {
    '@w_lib/init.lua',
    'shared/contract.lua',
    'config.lua',
}

client_scripts {
    'client/main.lua',
}

server_scripts {
    'server/main.lua',
}

files {
    'locales/*.json',
}

w_lib {
    'locale',
}
```

> **Note:** Including `w_lib { 'locale' }` automatically loads `locales/<lang>.json` on both client and server using the `locale('key')` global helper.

---

## 2. `shared/contract.lua`

Keep every network event and callback name in one shared table so client and server never drift out of sync:

```lua
MyContract = {
    CB = {
        BUY_COFFEE = 'my_resource:cb:buyCoffee',
    },
}
```

---

## 3. `locales/en.json`

```json
{
  "prompt_buy_coffee": "Buy Hot Coffee ($2)",
  "bought_coffee": "You bought a hot coffee for $%s.",
  "not_enough_cash": "You do not have enough cash.",
  "bag_full": "Your satchel is full."
}
```

---

## 4. `server/main.lua`

Always validate distance, money, and inventory capacity on the **server**:

```lua
local WCore = exports.w_core:GetCoreObject()

local COFFEE_SPOT = vec3(-313.5, 806.2, 118.98)
local COFFEE_PRICE = 2

w.callback.register(MyContract.CB.BUY_COFFEE, function(source)
    local player = WCore.Functions.GetPlayer(source)
    if not player then return false, 'no_player' end

    -- 1. Server-side distance check
    local ped = GetPlayerPed(source)
    if #(GetEntityCoords(ped) - COFFEE_SPOT) > 5.0 then
        return false, 'too_far'
    end

    -- 2. Check inventory space before taking money
    if not exports.w_inventory:CanCarryItem(source, 'consumable_coffee', 1) then
        return false, 'bag_full'
    end

    -- 3. Take money server-side
    if not player.Functions.RemoveMoney('cash', COFFEE_PRICE, 'coffee-stand') then
        return false, 'not_enough_cash'
    end

    -- 4. Give item
    local added = exports.w_inventory:AddItem(source, 'consumable_coffee', 1)
    if not added then
        player.Functions.AddMoney('cash', COFFEE_PRICE, 'coffee-stand-refund')
        return false, 'bag_full'
    end

    return true, COFFEE_PRICE
end)
```

---

## 5. `client/main.lua`

Use **`J`** as the interaction key (never `E`, which chokes nearby players in RDR2) and use `w.prompt` + `w.notify`:

```lua
local COFFEE_SPOT = vec3(-313.5, 806.2, 118.98)

CreateThread(function()
    w.prompt.at(COFFEE_SPOT, {
        key = 'J',
        label = function() return locale('prompt_buy_coffee') end,
        distance = 2.5,
        onComplete = function()
            local ok, res = w.callback.await(MyContract.CB.BUY_COFFEE, false)
            if ok then
                w.notify({
                    description = locale('bought_coffee', res),
                    type = 'success',
                })
            else
                w.notify({
                    description = locale(res == 'bag_full' and 'bag_full' or 'not_enough_cash'),
                    type = 'error',
                })
            end
        end,
    })
end)
```

---

## Best Practices Checklist

- **Never call `exports.w_core:GetCoreObject()` inside a loop or per-event handler.** Fetch it once at the top of the file (it copies the entire core table).
- **Use `w.callback.register` / `w.callback.await`** instead of pairing separate `TriggerServerEvent` + `TriggerClientEvent`.
- **Use `w.glow` instead of `DrawMarker`** for world highlights (better RedM visuals and performance).
- **Avoid routine `print()` calls:** `w_lib` prints one startup banner per resource and drops non-error `print()` lines unless `consoleDebug` is enabled.
- **Database access:** Use `exports.w_lib:DbQuery / DbSingle / DbScalar / DbInsert / DbUpdate / DbTransaction` or `@oxmysql/lib/MySQL.lua`.
