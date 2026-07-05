---
name: project_forest_mob_models
description: Forest (biome 1) mob models swapped to meme characters + camp sign/name fixes; 2026-07-05
metadata: 
  node_type: memory
  type: project
  originSessionId: 01b9fb35-a11d-44d0-a242-69af7f86c341
---

Forest biome mobs re-modeled 2026-07-05 (verified: 24 tagged/alive/PrimaryPart, no play errors; visual screenshot NOT confirmed — capture tool was timing out).

**Mobs are pre-placed models** in `Workspace.ForestBiome.Camps.Camp_<ID>.Mobs` (6 per camp), tagged `ForestSlime` (CollectionService). Combat/movement (`ServerScriptService.ForestMobs`) + client idle (`StarterPlayerScripts.ForestSlimeIdle`) only need: tag, `PrimaryPart`, and attributes `BasePivot`,`HomePivot`,`Phase`,`MaxHealth`,`AttackPower`,`Armor`,`Level`,`CampId`,`CrystalDrop`. Idle just re-pivots the whole model around `BasePivot`. Targeting (weapon ClientAttack) uses mouse.Target OR auto-aim nearest tagged model within reach — so part CanQuery doesn't break combat.

**Swapped slimes → meme models** (Camp→asset, mapped by camp order GREEN/YELLOW/BLUE/RED):
- GREEN Mossling → **Skibidi Toilet** (asset 74955058886164)
- YELLOW Sunpip → **Aram Ram Ramar** (106430366541219)
- BLUE Dewdrop → **Trippi Troppi Troppa Trippa** (132790929973448)
- RED Cinder → **Tralalero Tralala** (83018340261801)

**How:** `InsertService:LoadAsset` FAILED ("User is not authorized") but the `insert_asset` MCP tool (Toolbox path) worked. Migration (run once via execute_luau in Edit): per model — remove scripts/Humanoids, `Model:ScaleTo(5.5/heightY)` (normalize height ~5.5), anchor all parts + CanCollide=false + Massless, add invisible `Root` part at bbox center as PrimaryPart. Then per old mob: copy attributes + tag, name=new mob name, set BasePivot/HomePivot to feet-on-ground (groundY from old bbox bottom + halfHeight, keep old yaw), PivotTo, destroy old.

**Crystal-drop check (user asked):** drops resolve via mob `CrystalDrop` attr (GREEN/YELLOW/BLUE/RED) → `GameData.Crystals[id].name`, so they correctly show the current names: GREEN=Verdant Star, YELLOW=Radiant Star, BLUE=Tidal Star, RED=Ember Star. Fixed stale strings that still said "Verdant/Amber/Tidal/Ember Crystal": mob `CrystalName` attr, `ForestConfig.CAMPS[].crystal`+`.mob`, and the camp Sign BillboardGui TextLabels ("◈ <name>"). See [[project_inventory_friends_robux]], [[project_forest_biome_build]].

**OPEN:** couldn't screenshot to confirm scale/orientation look right (Skibidi came out flat/wide ~9.4x5.5x1.7). If any model looks lying-down or wrong size, adjust the template scale/rotation and re-run the swap.
