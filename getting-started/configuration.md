# Configuration & Permissions

## Key Server Convars (`server.cfg`)

| Convar | Default | Description |
| :--- | :--- | :--- |
| `setr w_lib:locale` | `"en"` | System-wide default language (`"en"` or `"ar"`). Players can also switch locally via `/wsettings` or `/w_lib`. |
| `setr w_inventory:defaultShops` | `0` (`1` in recipe) | Enables the built-in `w_inventory` General Stores across major towns (Valentine, Rhodes, Saint Denis, Strawberry, Blackwater, Tumbleweed, Armadillo, Van Horn). |
| `setr w_loading:serverName` | `"Wraith Core"` | Browser title and accessibility label for the loading screen and curtain. |
| `setr w_loading:brandLatin` | `"WRAITH CORE"` | Main Latin wordmark displayed on the loading screen and curtain. |
| `setr w_loading:brandArabic` | `"ريث كور"` | Arabic wordmark shown when the loading screen locale is Arabic. |
| `set w_ranks:masters` | `""` | Comma-separated identifiers (`license:...`, `steam:...`, `discord:...`) that automatically receive the `master` rank on every boot. |
| `setr w_lib:consoleDebug` | `0` | Set to `1` to re-enable verbose `print()` output across all `w_lib` resources (or `setr <resource>:consoleDebug 1` for one resource). |
| `setr w_lib:glowBudget` | `150` | Maximum concurrent `w.glow` particle points per resource. |

---

## Ranks & ACE Permissions

Wraith Core supports both standard FXServer ACE groups (`group.god`, `group.admin`, `group.mod`) and database-backed staff ranks managed by `w_core/server/ranks.lua` (`w_ranks` & `w_rank_members`).

### Granting Master / Owner Access

Add your Rockstar license (or Steam/Discord ID) in `server.cfg`:

```cfg
set w_ranks:masters "license:0123456789abcdef0123456789abcdef01234567"
```

You can also assign standard ACE groups in `permissions.cfg`:

```cfg
add_principal identifier.license:0123456789abcdef0123456789abcdef01234567 group.god
```

### Permission Hierarchy

```text
group.god  ──▶  inherits  ──▶  group.admin  ──▶  inherits  ──▶  group.mod
```

By default, `permissions.cfg` grants `group.admin` access to gameplay commands while explicitly denying dangerous server-control commands (`add_ace`, `add_principal`, `exec`, `set`, `ensure`, `stop`, `restart`, `quit`, `sv_licenseKey`) so in-game admins cannot escalate privileges.

---

## Built-In Inventory Shops

In the public Wraith Core stack, shops are powered directly by **`w_inventory`**:

1. **General Stores:** Controlled by `setr w_inventory:defaultShops 1` in `server.cfg` (defined in `w_inventory/data/shops.lua`).
2. **Police Armory:** Built into `w_inventory/data/shops.lua` (`armory_police`) and restricted to police jobs.
3. **Hospital Pharmacy:** Registered dynamically by `w_medic` via `exports.w_inventory:RegisterShop(...)` when `Config.Pharmacy.useStore = false` (the default).

To add your own custom shop, either add an entry in `w_inventory/data/shops.lua` or call `exports.w_inventory:RegisterShop` from any server script — see [w_inventory Documentation](core/w_inventory.md#shops).

---

## Language & Localization

Every Wraith Core resource ships with both `locales/en.json` and `locales/ar.json`.

- Set the server default in `server.cfg`:
  ```cfg
  setr w_lib:locale "en"   # or "ar"
  ```
- Players can switch their own client language live with `/wsettings`.
- All resources listen to the `w_lib:setLocale` event and re-translate UI, prompts, and blips dynamically.

---

## Important `.cfg` Rule

Never put a semicolon (`;`) inside a comment in `server.cfg`, `permissions.cfg`, or `voice.cfg`. FXServer splits console lines on `;` even after `#` and will attempt to execute the remaining text as a command.
