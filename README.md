# Wraith Core Documentation

**Wraith Core** is a modern, modular, open-source **RedM (Red Dead Redemption 2)** roleplay framework developed by **Wraith Studio**.

Designed around a unified library ([`w_lib`](https://github.com/Wraith-Core/w_lib)), server-authoritative architecture, built-in multilingual support (English & Arabic), and clean Lua 5.4 codebases, Wraith Core gives server owners a complete playable foundation and gives developers clean, consistent APIs to build on.

---

## Quick Links

- **One-Click txAdmin Recipe:** [`Wraith-Core/txAdminRecipe`](https://github.com/Wraith-Core/txAdminRecipe)
  ```text
  https://raw.githubusercontent.com/Wraith-Core/txAdminRecipe/main/wraithcore.yaml
  ```
- **Core UI & Utility Library:** [`Wraith-Core/w_lib`](https://github.com/Wraith-Core/w_lib)
- **Framework Core:** [`Wraith-Core/w_core`](https://github.com/Wraith-Core/w_core)
- **Inventory & Built-In Shops:** [`Wraith-Core/w_inventory`](https://github.com/Wraith-Core/w_inventory)
- **GitHub Organization:** [https://github.com/Wraith-Core](https://github.com/Wraith-Core)

---

## Free Open-Source Stack (19 Resources)

| Resource | Category | Role |
| :--- | :--- | :--- |
| [`w_lib`](https://github.com/Wraith-Core/w_lib) | Core | Unified UI, callbacks, prompts, placer, glow, blips, DB helpers, framework bridge, locales |
| [`w_core`](https://github.com/Wraith-Core/w_core) | Core | Characters, money, jobs, gangs, metadata, ranks/ACE, bans, commands |
| [`w_inventory`](https://github.com/Wraith-Core/w_inventory) | Core | Slot + metadata inventory, weapons, stashes, crafting, built-in shops |
| [`w_adminmenu`](https://github.com/Wraith-Core/w_adminmenu) | Admin | Open-source admin menu built with `w_lib` interfaces (`/admin` or `F10`) |
| [`w_fasttravel`](https://github.com/Wraith-Core/w_fasttravel) | World | Stagecoach & fast travel station network across 10 RDR2 towns (`w_lib`) |
| [`w_chat`](https://github.com/Wraith-Core/w_chat) | Interface | Styled roleplay chat & command suggestions |
| [`w_loading`](https://github.com/Wraith-Core/w_loading) | Interface | Configurable RedM loading screen & transition curtain |
| [`w_creators`](https://github.com/Wraith-Core/w_creators) | Character | Character selector, creator, appearance persistence, death respawn |
| [`w_clothing`](https://github.com/Wraith-Core/w_clothing) | Character | Clothing stores, wardrobes, saved outfits, clothing toggles |
| [`w_barber`](https://github.com/Wraith-Core/w_barber) | Character | Barber chairs, hair, beards, eyebrows, makeup |
| [`w_hud`](https://github.com/Wraith-Core/w_hud) | Interface | Health, stamina, hunger, thirst, money, voice level, announcements |
| [`w_zones`](https://github.com/Wraith-Core/w_zones) | World | 100% native RDR2 zone announcement banner (zero NUI) |
| [`w_weathersync`](https://github.com/Wraith-Core/w_weathersync) | World | Server-synced weather, time, blackout, dynamic weather |
| [`w_interiors`](https://github.com/Wraith-Core/w_interiors) | World | Interior entity sets & IMAP loading across the map |
| [`w_doorlock`](https://github.com/Wraith-Core/w_doorlock) | World | Interactive door locks, job/item access, lockpicking, in-game editor |
| [`w_banking`](https://github.com/Wraith-Core/w_banking) | Economy | Personal savings accounts, gang accounts, transfers, statements |
| [`w_management`](https://github.com/Wraith-Core/w_management) | Economy | Job & gang boss funds management |
| [`w_policejob`](https://github.com/Wraith-Core/w_policejob) | Jobs | Law enforcement: duty, cuffing, escorting, jailing, evidence, armory |
| [`w_medic`](https://github.com/Wraith-Core/w_medic) | Jobs | Doctors & hospitals: wounding, last stand, revive, hospital beds, pharmacy |

> **Design Choices in the Free Stack:**
> - **Shops** are handled directly by [`w_inventory`](core/w_inventory.md) (including General Stores, Police Armory, and Hospital Pharmacy).
> - **Pause Menu** uses Red Dead Redemption 2's built-in native pause menu.
> - **Zone Banners** use [`w_zones`](resources/interface-world.md#w_zones) (native RDR2 location shard, zero NUI).

---

## Core Design Principles

1. **Single Library (`w_lib`):** Every resource loads `shared_script '@w_lib/init.lua'` and shares one consistent UI theme, callback layer, prompt engine, and localization system.
2. **Server Authority:** Money, inventory, permissions, and gameplay state are validated and mutated on the server.
3. **Standard Interaction Key (`J`):** Wraith Core uses **`J`** for world interactions instead of `E` (in RDR2, `E` grabs/chokes nearby players).
4. **Quiet Console:** `w_lib` suppresses routine console noise and prints one clean startup banner per resource, keeping errors and warnings visible.
5. **Bilingual Ready:** English (`en`) is the default in the public recipe; Arabic (`ar`) is built into every core resource and switchable live.

---

## Next Steps

- Start a server in 5 minutes with the [Installation Guide](getting-started/installation.md).
- Configure ranks, language, and shops in [Configuration & Permissions](getting-started/configuration.md).
- Build your own script with [Creating a Resource](getting-started/creating-a-resource.md).
