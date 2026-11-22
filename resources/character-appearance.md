# Character & Appearance Resources

This group manages the full player identity lifecycle: character selection, character creation, death resurrection, clothing stores, wardrobes, and barber shops.

---

## `w_creators`

**Repository:** [`Wraith-Core/w_creators`](https://github.com/Wraith-Core/w_creators)  
**Dependencies:** `w_lib`, OneSync

The character selector, creator studio, appearance persistence engine, speaking voice selector, and single authoritative death/resurrection entry point.

### Key Features
- Multi-character selection & creation studio with camera controls.
- Full RedM ped appearance schema (`characters_appearence` table, auto-migrated).
- Character speaking voice & VOFX tone selection.
- Authoritative resurrection export used by `w_medic` and admin revives.
- Seamless integration with `w_clothing`, `w_barber`, and `w_loading`.

### Exports

```lua
-- Resurrection (Client — single authoritative entry point)
exports.w_creators:Resurrect(coords, heading, reason)

-- Appearance & Outfits
local appearance = exports.w_creators:GetAppearance()
local outfit     = exports.w_creators:GetCurrentOutfit()
exports.w_creators:RefreshAppearance()
local inScene    = exports.w_creators:IsInCharacterScene()

-- Clothing Toggles
exports.w_creators:ToggleHat()
exports.w_creators:ToggleBandana()
exports.w_creators:ToggleSleeves()
exports.w_creators:SetClothingState(category, state)

-- Open Services (delegates to w_clothing / w_barber when installed)
exports.w_creators:OpenClothingStore(src, locationId)
exports.w_creators:OpenWardrobe(src, locationId)
exports.w_creators:OpenBarber(src, locationId)
exports.w_creators:OpenCosmeticSurgery(src, locationId)
```

### Useful Commands
- `/characters` — Return to character selection (if allowed by config).
- `/reloadappearance` — Re-apply the saved character appearance to the player ped.

---

## `w_clothing`

**Repository:** [`Wraith-Core/w_clothing`](https://github.com/Wraith-Core/w_clothing)  
**Dependencies:** `w_lib`, `oxmysql`, OneSync

Standalone clothing stores, personal wardrobes, owned clothing catalog, colorway tints, and saved outfits.

### Key Features
- Interactive clothing stores across RDR2 towns (press **`J`**).
- Persistent owned wardrobe (`w_creators_clothes`, `w_creators_outfits`, `w_creators_colorways`, `w_clothing_states`).
- Save, rename, equip, and delete complete outfits.
- Clothing state toggles (hat, bandana, rolled sleeves, collar, coat open/closed).

### Exports

```lua
exports.w_clothing:OpenClothingStore(src, locationId)
exports.w_clothing:OpenWardrobe(src, locationId)
local busy    = exports.w_clothing:IsBusy()
local outfits = exports.w_clothing:GetOutfits()
local owned   = exports.w_clothing:GetOwnedClothes()
local bandana = exports.w_clothing:IsBandanaUp()
```

---

## `w_barber`

**Repository:** [`Wraith-Core/w_barber`](https://github.com/Wraith-Core/w_barber)  
**Dependencies:** `w_lib`, `oxmysql`, OneSync

Interactive RedM barber shops with animated barber chairs, hairstyles, beards, eyebrows, teeth, and makeup.

### Key Features
- Authentic RDR2 barber chair seating (`PROP_PLAYER_BARBER_SEAT`).
- Full hair, facial hair, eyebrow, and makeup customization synced to the character's appearance.
- Configurable locations stored in `w_creators_locations`.

### Exports

```lua
exports.w_barber:OpenBarber(src, locationId)
local busy = exports.w_barber:IsBusy()
```
