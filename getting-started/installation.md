# Installation

## Requirements

- **FXServer Artifact:** Build `1491`+ (`rdr3`, OneSync enabled) — `w_lib` requires FXServer `14901` or newer.
- **Database:** MariaDB 10.6+ or MySQL 8.0+ (UTF-8 / `utf8mb4_general_ci`).
- **Cfx.re License Key:** Generate one at [portal.cfx.re](https://portal.cfx.re).
- **Dependencies:**
  - [`oxmysql`](https://github.com/overextended/oxmysql/releases/latest)
  - [`pma-voice`](https://github.com/AvarianKnight/pma-voice)

---

## Method 1: txAdmin Recipe (Recommended)

The fastest and safest way to deploy Wraith Core is using the official **txAdmin Recipe**:

1. Download and start **FXServer**, then open the **txAdmin** web interface.
2. When prompted for a deployment type, select **Remote URL Template**.
3. Enter the official Wraith Core recipe URL:
   ```text
   https://raw.githubusercontent.com/Wraith-Core/txAdminRecipe/main/wraithcore.yaml
   ```
4. Enter your database credentials (`host`, `user`, `password`, `database`) and your Cfx.re license key.
5. Click **Run Recipe**. txAdmin will automatically:
   - Download Cfx.re base RedM resources (`mapmanager`, `spawnmanager`, `basic-gamemode`).
   - Install `oxmysql` and `pma-voice`.
   - Clone all 17 open-source Wraith Core resources into `resources/[wraith]/`.
   - Import all required SQL schemas into your database.
   - Generate `server.cfg`, `permissions.cfg`, and `voice.cfg`.
6. Before starting, set your master identifier in `server.cfg` (see [Configuration](getting-started/configuration.md)).

---

## Method 2: Manual Installation

1. Create a fresh RedM `txData` server directory with `mapmanager`, `spawnmanager`, and `basic-gamemode`.
2. Download the latest release of [`oxmysql`](https://github.com/overextended/oxmysql/releases/latest) and [`pma-voice`](https://github.com/AvarianKnight/pma-voice) into `resources/[standalone]/`.
3. Clone all 17 resources from `https://github.com/Wraith-Core/<resource>` into `resources/[wraith]/`:
   ```bash
   mkdir -p resources/[wraith] && cd resources/[wraith]
   for repo in w_lib w_core w_inventory w_chat w_loading w_creators w_clothing w_barber w_hud w_zones w_weathersync w_interiors w_doorlock w_banking w_management w_policejob w_medic; do
     git clone "https://github.com/Wraith-Core/${repo}.git"
   done
   ```
4. Import the SQL files into your database in this order:
   1. `w_core/wcore.sql`
   2. `w_creators/sql/install.sql`
   3. `w_clothing/sql/install.sql`
   4. `w_barber/sql/install.sql`
   5. `w_hud/sql/w_hud_needs.sql`
   6. `w_hud/sql/w_hud_satchels.sql`
   7. `w_hud/sql/w_hud_server.sql`
   8. `w_hud/sql/w_hud_settings.sql`
   9. `w_banking/w_banking.sql`
   10. `w_management/w_management.sql`
   *(Note: `w_inventory`, `w_doorlock`, and `w_core` rank/citizen tables auto-create any remaining tables on first boot.)*
5. Copy `server.cfg`, `permissions.cfg`, and `voice.cfg` from [`Wraith-Core/txAdminRecipe`](https://github.com/Wraith-Core/txAdminRecipe) into your server data root and fill in your license key and `mysql_connection_string`.

---

## Resource Start Order

Ensure order matters. Always boot resources in this sequence:

```cfg
exec permissions.cfg
exec voice.cfg

ensure mapmanager
ensure spawnmanager
ensure basic-gamemode

ensure oxmysql
ensure pma-voice

ensure w_lib
ensure w_core
ensure w_inventory
ensure w_chat
ensure w_loading

ensure w_creators
ensure w_clothing
ensure w_barber
ensure w_hud
ensure w_zones

ensure w_weathersync
ensure w_interiors
ensure w_doorlock

ensure w_banking
ensure w_management
ensure w_policejob
ensure w_medic
```

> **Important:** `w_lib` must start **before** every other `w_*` resource, and `oxmysql` must start before `w_lib`.
