# Economy & Core Roleplay Jobs

---

## `w_banking`

**Repository:** [`Wraith-Core/w_banking`](https://github.com/Wraith-Core/w_banking)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`, `oxmysql`

Complete RedM banking system with tellers across major towns, personal savings accounts, gang accounts, transfers, transaction statements, and `/givecash`.

### Database Tables (`w_banking.sql`)
- `bank_accounts` — Savings and gang account balances
- `bank_statements` — Deposit, withdrawal, and transfer ledger

### Exports

```lua
exports.w_banking:openAccountScreen()
exports.w_banking:createSavingsAccount(citizenid)
exports.w_banking:createGangAccount(gangId)
local banks = exports.w_banking:getBanks()
```

### Commands
- `/givecash <id> <amount>` — Hand cash directly to a nearby player.

---

## `w_management`

**Repository:** [`Wraith-Core/w_management`](https://github.com/Wraith-Core/w_management)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Society & gang treasury management (`management_funds` table) for boss menus and faction funding.

### Server Exports

```lua
-- Job accounts
local balance = exports.w_management:GetAccount('police')
exports.w_management:AddMoney('police', 500)
local ok = exports.w_management:RemoveMoney('police', 100)

-- Gang accounts
local gangBal = exports.w_management:GetGangAccount('example')
exports.w_management:AddGangMoney('example', 250)
local okGang = exports.w_management:RemoveGangMoney('example', 50)
```

---

## `w_policejob`

**Repository:** [`Wraith-Core/w_policejob`](https://github.com/Wraith-Core/w_policejob)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Core law enforcement system for Sheriffs and Marshals.

### Key Features
- On-duty / off-duty toggle and sheriff office stations.
- Built-in Police Armory via `w_inventory` (`armory_police` in `w_inventory/data/shops.lua`).
- Handcuffing (server-validated `ishandcuffed` metadata), escorting, searching, seizing cash, jailing, and unjailing.
- Blood/casing/DNA evidence collection and cleanup.
- Placeable law enforcement props (`/pobject`).

### Exports

```lua
local cuffed = exports.w_policejob:IsHandcuffed()
```

### Commands
- `/cuff`, `/escort`, `/jail`, `/unjail`, `/seizecash`, `/callsign`, `/sc`, `/pobject`, `/takedna`, `/clearblood`, `/clearcasings`

---

## `w_medic`

**Repository:** [`Wraith-Core/w_medic`](https://github.com/Wraith-Core/w_medic)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`

Full medical & hospital roleplay system for the **`doctor`** job (grades `0`–`5`).

### Key Features
- Wounding, bleeding, last-stand, death timer, and hospital respawn flow (integrated with `w_creators:Resurrect`).
- Hospital beds, treatment actions, revives, and medical status inspection.
- **Built-In Inventory Pharmacy:** Uses `w_inventory:RegisterShop` out of the box (`Config.Pharmacy.useStore = false`), providing doctor-only access to bandages, painkillers, and first-aid kits with timed restocking.
- On-site doctor hiring/ranking terminal at hospital desks.

### Exports

```lua
local dead   = exports.w_medic:IsDead()
local inBed  = exports.w_medic:InHospitalBed()
exports.w_medic:RevivePlayer(source)
exports.w_medic:HealPlayer(source)
exports.w_medic:EndBedStay()
```

### Commands
- `/911e` — Send an emergency distress alert to on-duty doctors.
- `/treat`, `/status`, `/medrevive`, `/revivep`, `/aheal`, `/kill`, `/pharmacy`

---

## `w_hunting`

**Repository:** [`Wraith-Core/w_hunting`](https://github.com/Wraith-Core/w_hunting)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`, `w_core`, `w_inventory`

Butcher & hunting economy across 10 RDR2 towns (Valentine, Strawberry, Blackwater, Saint Denis, Rhodes, Annesburg, Van Horn, Armadillo, Tumbleweed, Riggs Station), built 100% with **`w_lib`**.

- Sell whole animal carcasses carried on your shoulder or strapped to your nearby horse (quality multipliers across 68 animal models).
- Automatic wild skinning/plucking loot (`EVENT_LOOT_COMPLETE`) into `w_inventory`.
- Sell meats, pelts, hides, antlers, horns, feathers, and critters individually or via one-click **Sell All**, with a rotating **+25% Daily High Demand** bonus.
- Built-in hunting supplies shop (knife, lasso, bow, arrows).

---

## `w_fishing`

**Repository:** [`Wraith-Core/w_fishing`](https://github.com/Wraith-Core/w_fishing)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`, `w_core`, `w_inventory`

Native RDR2 fishing minigame (`TASK_FISHING_MIXIN`) and 8 fishmonger stalls across the map, built 100% with **`w_lib`**.

- Equip `WEAPON_FISHINGROD` and attach bait either by using a bait item from `w_inventory` or pressing **`J`** to open the quick bait menu.
- Live `lib.showTextUI` controls & line tension monitor, native audio (`MGFSH_Sounds`), struggle water splashes, and Keep/Release controls across 27 RDR2 fish species.
- Fishmonger stalls let players buy rods & baits/lures, sell caught fish, or fillet fish into `fishmeat`.

---

## `w_mining`

**Repository:** [`Wraith-Core/w_mining`](https://github.com/Wraith-Core/w_mining)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`, `w_core`, `w_inventory`

Mining camps, wild rock mining, ore crushing, gold washing, and smelting furnaces built 100% with **`w_lib`**.

- 32 ore & salt rock veins across Annesburg, Gaptooth Breach, and Grizzlies mines plus dynamic wild rock mining outside towns (`J` key + optional `lib.skillCheck` bonus).
- 4 Smelting Furnaces & Mining Camps (Annesburg, Valentine, Gaptooth, Grizzlies): smelt Iron, Copper, Lead, Silver, Steel, and Gold bars, crush raw rocks into ores/gems, wash gold nuggets, sell minerals/ingots, and buy pickaxes.

---

## `w_lumberjack`

**Repository:** [`Wraith-Core/w_lumberjack`](https://github.com/Wraith-Core/w_lumberjack)  
**License:** GPL-3.0  
**Dependencies:** `w_lib`, `w_core`, `w_inventory`

Forest woodcutting and sawmills built 100% with **`w_lib`**.

- Chop 130+ wild RDR2 tree models across forests outside restricted towns (`J` key + `p_axe02x` animation + optional `lib.skillCheck` bonus) to gather timber logs, wood, bark, and tree sap.
- 4 Sawmills (Appleseed Timber Co., Strawberry, Lemoyne Timber Yard, Annesburg): mill timber logs into planks (`wood_plank`), split firewood (`wood`), sell lumber goods, and buy felling axes.
