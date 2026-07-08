---
name: project_inventory_friends_robux
description: "Inventory redesign (12 crystals; star/gem icons runtime-rendered from picked designs since 2026-07-07), crystal rename Colour+Shape, friends buff, shop Robux icon"
metadata: 
  node_type: memory
  type: project
  originSessionId: 01b9fb35-a11d-44d0-a242-69af7f86c341
---

Hub UI + crystal overhaul, built 2026-07-05 (verified: play test ran with no script errors).

**Crystals: themed names, one shape per biome, 4 canonical colours (green/yellow/blue/red).**
In `ReplicatedStorage.GameData` (`GameData.Crystals`) each crystal has `color` (green/yellow/blue/red only), themed `name`, `shape` (star/gem/crystal), and an unused `icon` field. Ids unchanged (save-compat), mapped in order to green/yellow/blue/red per biome. Themed names: Forest star = Verdant/Radiant/Tidal/Ember Star; Desert gem = Jade/Amber/Azure/Garnet Gem; Hell crystal = Venom/Sulfur/Frost/Magma Crystal. Names flow automatically to chest drops, wheel prizes, rebirth (all read `.name`).

**UPDATE 2026-07-07 — star + gem icons are now RUNTIME-RENDERED images.** User re-picked the flaticon-style designs (blue 4-point sparkle star, purple faceted diamond gem). New `ReplicatedStorage.CrystalIconImages` ModuleScript rasterizes both shapes into 128px `EditableImage`s at runtime, one per crystal colour (baked colours, so highlights go LIGHTER than base — multiply-tint never could), cached + warmup() on client start (8 icons ≈ 0.3s). `makeCrystalIcon` uses `CrystalIconImages.get(cd)` and falls back to the old vector code if EditableImage unavailable; Hell "crystal" shape still code-drawn (no design picked). Rebirth cost cells reuse makeCrystalIcon → also upgraded. REQUIRED: "Allow Mesh & Image APIs" toggle in Game Settings → Security (user flipped 2026-07-07; without it get() warns + falls back). CreateAssetAsync + upload_image(local paths) both unavailable in this Studio — that's why runtime rendering. PNG/SVG masters + tint sheet in repo `crystal-icons/`. Geometry authored in 512-space, matches the SVGs.

**OLD (pre-2026-07-07) — icons were CODE-DRAWN, not PNGs.** The user's uploaded shape assets (star=decal 84260632639303 pre-coloured BLUE, gem=decal 109884124449437 pre-coloured PURPLE, crystal image 678696926 [note: 678696930 is the decal wrapper and renders BLANK in ImageLabel] pre-coloured BLUE) could NOT be tinted to clean red/yellow/green — ImageColor3 multiply only darkens, so a blue source can never become red. So `ForestInventoryUI` has `makeCrystalIcon(parent, cd, S)` that draws a vector star (8-point sparkle), gem (rotated-square diamond + facet), or crystal (3 shard bars) in exactly `cd.color`. Used in both the bag grid and the rebirth cost cells. If ever switching back to pngs, they must be WHITE/greyscale silhouettes to tint properly.

**Inventory (BAG) now shows ALL 12 crystals**, not just current biome's 4. `ForestInventoryUI` BAG panel enlarged to 600x620; crystal grid is 4x3 (rows = biomes) via `buildCrystalCells(GameData.AllCrystalOrder)`; per-band rebuild removed from onSync (only counts update). Rebirth cost cells still show current biome only (correct) but use the same icons.

**Friends buff** — new `ServerScriptService.FriendBuffService`: counts a player's Roblox friends currently in-server (cached `IsFriendsWith`), sets attributes `FriendCount`, `FriendGoldBonus` = 0.05*min(count,4), and `FriendCrystalBonus` = 0.05*min(count,4). So each active friend gives +5% gold AND +5% crystal drop chance (additive, cap +20% each). Mob `die()` in Forest/Desert/HellMobs adds `+ FriendGoldBonus` into the gold multiplier and `* (1 + FriendCrystalBonus)` onto dropChance. Constants: `GameData.FriendGoldPerFriend=0.05`, `GameData.FriendCrystalPerFriend=0.05`, `GameData.FriendBuffCap=4`. Panel shows two badges: "+X% GOLD" (green) and "+X% CRYSTAL DROP" (blue). Client `StarterPlayerScripts.FriendsPanel` (LocalScript): toggle button (icon rbxassetid://95687051984929) strictly in bottom-right corner with an online-count badge; clicking opens/closes a panel that opens just above it (also has an X close). Panel lists in-server friends w/ headshots + "+X% GOLD" badge. HP bar (in ForestInventoryUI) moved to bottom-CENTER, 300x40 (anchor 0.5,1 @ y -18).

**Shop Robux items** — in `ForestInventoryUI` shop, Robux-purchasable rows (cosmetics) now show the Robux logo (`rbxassetid://125721496459208`, ROBUX_ICON) via an ImageLabel inside the buy button + numeric price, replacing the "R$" text. Gold items keep "$". See [[project_inventory_ui_icons]], [[project_hub_systems]], [[project_wheel_of_fortune]].
