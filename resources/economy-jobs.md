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
