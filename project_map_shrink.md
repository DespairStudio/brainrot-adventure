---
name: project_map_shrink
description: "2026-07-07: all 3 biomes uniformly scaled 0.75 about their centers (camps ~25% closer) to cut travel time between monster camps"
metadata: 
  node_type: memory
  type: project
  originSessionId: c8c7e304-5e81-413a-b1fb-c0e39352dab4
---

User: "shrink the maps a little — takes too much time to explore between monster camps." Chose **~25% closer**. Applied a uniform **0.75× similarity scale about each biome's CENTER** to all three biome folders — `Workspace.ForestBiome` (center -3500,0,0), `Workspace.GildedWastes` (7000,0,0), `Workspace.HellRealm` (0,-3000,0). Method: wrap the folder's children in a temp Model, set `WorldPivot = CFrame.new(CENTER)`, `Model:ScaleTo(0.75)`, unwrap, destroy the temp Model. A uniform scale is a similarity transform, so paths/props stay aligned and nothing can overlap; camps, props and mobs are all 25% smaller and 25% closer to center. Verified numerically: camp distance from center **600 → 450** in all three; biome bbox ×0.75.

**Stale runtime anchors fixed (critical):** mobs are baked, tagged instances (`ForestSlime` / `DesertMob` / `HellMob`, 24 each = 72) that remember home in a **`BasePivot` CFrame ATTRIBUTE**. `ScaleTo` moves parts but NOT custom attributes, so the mobs would teleport back to the old ~600 spot at runtime (the movement brain PivotTo's BasePivot). Fixed by setting each mob's `BasePivot` (and `HomePivot` if present) = `mob:GetPivot()` after scaling — re-verified forest sample 587 → 440. Combat ranges live in GameData constants and did NOT scale, so mobs are smaller but still hittable at the same reach.

**Configs synced** (`ReplicatedStorage.ForestConfig/DesertConfig/HellConfig` — source of truth for minimap markers, teleport arrival, zone detection, fog): every spatial scalar ×0.75 — GLADE_R 140→105, CAMP_DIST 600→450, CAMP_PAD_R 95→71.25, VALLEY_R 820→615, WALL_R 870→652.5, MOUNTAIN_R 1000→750, ZONE_R 1300→975, LIGHTING FogStart/FogEnd ×0.75 — plus every position (ARRIVAL, RETURN_PAD, POND/OASIS/LAVA, all four CAMPS[].pos per biome) scaled about center. Left unchanged: CENTER, GROUND_Y, HUB_SPAWN (hub isn't scaled), MOB_STATS, BIOME bands, ENTRY_REBIRTH, colors.

**Consumers checked before editing:** only the Minimap scripts read `cfg.CAMPS.pos`; `CAMP_DIST` and `cfg.CAMPS` are NOT used by mob spawning (mobs are baked/tagged, not built from config). No script rebuilds a biome at runtime — `ForestBiome`/`GildedWastes`/`HellRealm` are only `FindFirstChild`'d by HubPortals + the ChestSpawn scripts, never reconstructed — so the baked (now-scaled) world + scaled configs are the whole change, no duplication risk.

**CAVEAT for future sessions:** the coordinates/radii written in [[project_forest_biome_build]], [[project_desert_biome_build]], [[project_hell_biome_build]] are PRE-shrink — multiply by 0.75, or just read the live config. Verified by geometry/coords because the `screen_capture` MCP tool timed out all session (no screenshots).
