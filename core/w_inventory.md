# w_inventory — Inventory, Weapons, Stashes & Shops

**Repository:** [`Wraith-Core/w_inventory`](https://github.com/Wraith-Core/w_inventory)  
**Dependencies:** `oxmysql`, `w_lib`, OneSync

`w_inventory` is a server-authoritative, slot-and-metadata inventory system for RedM. It handles player satchels, weapon durability/components/ammo, stashes, ground drops, crafting, usable items, and **built-in shops**.

---

## Item Definitions (`data/items.lua`)

Items are defined in `w_inventory/data/items.lua` with bilingual labels (`en` & `ar`), weights (in grams), stacking rules, and images (`web/images/`):

```lua
["consumable_coffee"] = {
    label = { en = "Hot Coffee", ar = "قهوة ساخنة" },
    weight = 250,
    stack = true,
    close = true,
    description = { en = "Freshly brewed hot coffee.", ar = "قهوة ساخنة طازجة." },
    client = {
        image = "consumable_coffee.png",
    },
},
```

---

## Server Exports

### Adding, Removing & Checking Items

```lua
-- Check carry capacity first
if exports.w_inventory:CanCarryItem(source, 'bandage', 3) then
    local ok, response = exports.w_inventory:AddItem(source, 'bandage', 3, { quality = 100 })
end

-- Remove items (optionally matching metadata or a specific slot)
local removed = exports.w_inventory:RemoveItem(source, 'bandage', 1)

-- Count items
local count = exports.w_inventory:GetItemCount(source, 'bandage')
local total = exports.w_inventory:Search(source, 'count', 'bandage')

-- Read slots & metadata
local items = exports.w_inventory:GetInventoryItems(source)
local slot  = exports.w_inventory:GetSlot(source, 1)
exports.w_inventory:SetMetadata(source, 1, { label = 'Custom Label', durability = 85 })
exports.w_inventory:SetDurability(source, 1, 75)
```

### Batch Operations

```lua
local lines = {
    { name = 'rdr_water_flask', count = 2 },
    { name = 'consumable_bread_roll', count = 2 },
}

if exports.w_inventory:CanCarryItems(source, lines) then
    exports.w_inventory:AddItems(source, lines)
end
```

### Stashes

```lua
-- Register a persistent stash
exports.w_inventory:RegisterStash('sheriff_locker', 'Sheriff Locker', 50, 100000, false, { police = 0 })

-- Create a temporary (in-memory) stash
local tempId = exports.w_inventory:CreateTemporaryStash({
    label = 'Loot Crate',
    slots = 20,
    maxWeight = 50000,
    items = { { 'bandage', 2 } },
})

-- Force a player to open a stash from the server
exports.w_inventory:forceOpenInventory(source, 'stash', 'sheriff_locker')
```

### Usable Items

```lua
exports.w_inventory:CreateUseableItem('bandage', function(source, item)
    -- Handle item usage on the server
    if exports.w_inventory:RemoveItem(source, 'bandage', 1, nil, item.slot) then
        TriggerClientEvent('my_script:client:useBandage', source)
    end
end)
```

### Inventory Hooks

Intercept and validate inventory actions before they complete:

```lua
local hookId = exports.w_inventory:registerHook('swapItems', function(payload)
    -- Return false to block the move
    return true
end, {
    print = false,
    itemFilter = { handcuffs = true },
})

-- Unregister later if needed
exports.w_inventory:removeHooks(hookId)
```

---

## Shops

`w_inventory` includes a complete server-authoritative shop engine (`modules/shops/`).

### 1. Built-In General Stores

Enable the preconfigured RedM General Stores in `server.cfg`:

```cfg
setr w_inventory:defaultShops 1
```

This spawns map blips and `J` walk-up prompts in **Valentine, Rhodes, Saint Denis, Strawberry, Blackwater, Tumbleweed, Armadillo, and Van Horn**, selling food, water, coffee, cigars, bandages, and lockpicks.

### 2. Static Shops (`data/shops.lua`)

Add custom shops directly to `w_inventory/data/shops.lua`:

```lua
my_hardware_shop = {
    name = 'Hardware Store',
    blip = { id = 'blip_shop_store', colour = 'BLIP_MODIFIER_MP_COLOR_32' },
    inventory = {
        { name = 'lockpick', price = 15, count = 50 },
        { name = 'bandage',  price = 5,  count = 100 },
    },
    locations = {
        vec3(-322.43, 803.79, 117.88),
    },
    -- Optional job restriction:
    -- groups = { police = 0 },
}
```

### 3. Dynamic Runtime Shops (`RegisterShop`)

Register a shop dynamically from any server resource (this is how `w_medic` registers hospital pharmacies):

```lua
-- Server
exports.w_inventory:RegisterShop('hospital_pharmacy', {
    name = 'Hospital Pharmacy',
    groups = { doctor = 0 },
    inventory = {
        { name = 'bandage',     price = 5,  count = 20 },
        { name = 'painkillers', price = 8,  count = 10 },
        { name = 'firstaid',    price = 15, count = 10 },
    },
})

-- Client (open the shop)
exports.w_inventory:openInventory('shop', { type = 'hospital_pharmacy' })
```

---

## Client Exports

```lua
exports.w_inventory:openInventory('stash', 'sheriff_locker')
exports.w_inventory:openInventory('shop', { type = 'general_store', id = 1 })
exports.w_inventory:closeInventory()

local items   = exports.w_inventory:GetPlayerItems()
local weight  = exports.w_inventory:GetPlayerWeight()
local maxW    = exports.w_inventory:GetPlayerMaxWeight()
local current = exports.w_inventory:GetCurrentWeapon()
```

---

## Admin Commands

| Command | Description |
| :--- | :--- |
| `/setitem <cid> <item> <count>` | Set item quantity for a player |
| `/removeitem <cid> <item> <count>` | Remove item from a player |
| `/clearinv <cid>` | Clear a player's inventory |
| `/viewinv <cid>` | Inspect another player's inventory |
| `/saveinv` | Force save active inventories to MySQL |
