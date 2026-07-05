---
name: project_biome_gear
description: Desert + Hell tier weapons/shields added to extend the gear ladder to rebirth 30
metadata: 
  node_type: memory
  type: project
  originSessionId: 92cbfddd-e486-407b-9ec7-e3d2663fbdf6
---

Added 4 weapons + 4 shields (2026-07-03) so the gear ladder covers Desert (reb 10-20) and Hell (reb 20-30) bands, not just Forest. Previously gear topped out at Crystal Scepter (60 atk, reqReb 5) while progression ran to reb 30.

Weapons (GameData.Weapons + WeaponOrder): CaramelKhopesh 95atk/reqReb10/22k gold (Epic), AmberGreatblade 150/reqReb15/48k (Legendary), MagmaMaul 230/reqReb20/120k (Legendary), BrimstoneScythe 360/reqReb25/260k (Mythic).
Shields (GameData.Shields + ShieldOrder): GildedAegis 26armor/reqReb10/16k (Epic), AmberBulwark 40/reqReb15/38k (Legendary), ObsidianWard 60/reqReb20/95k (Legendary), InfernalAegis 85/reqReb25/210k (Mythic).

All gold-only, gated by reqRebirth via existing ProfileService.BuyItem — no code changes to buy/equip/stats logic (fully data-driven). Added "Legendary"/"Mythic" colors to RARITY_COL in ForestInventoryUI.

Weapons need a Tool model in ServerStorage.Weapons named exactly the weapon's `name` (giveWeapon in PlayerInit resolves by name). Shields are purely data-driven — ShieldRig builds the held model from the `visual` block (only emblems heart/chips/hex are implemented; used hex). Calibrated so TTK stays ~0.7-1.8s and player survives 20-42 mob hits across bands. Extends [[project_balance_system]], [[desert_biome_build]], [[hell_biome_build]].

Model + icon swap (2026-07-03): 7 weapons (all but CandyCane starter) re-skinned to catalog assets. Each weapon in GameData has iconAsset=<id>; the in-hand Tool in ServerStorage.Weapons is the catalog mesh with our ClientAttack+ShieldRig grafted on (asset's own scripts + foreign sounds stripped). Mapping: LollipopHammer=7004608388 (Diamond Blade Sword), ChocolateBlade=14867566912 (Minecraft Sword, came as Accessory→wrapped into Tool, no Grip so may need grip tuning), CrystalScepter=14366040745 (Hammer), CaramelKhopesh=7894228999 (Staff of Azure), AmberGreatblade=7894558465 (Dark Matter Sword), MagmaMaul=7894419354 (Rainbow Star Sword), BrimstoneScythe=10288498712 (Azure Sword). GOTCHA: InsertService:LoadAsset fails ("not authorized") for catalog gear not owned by the place — use the MCP insert_asset tool instead. UI: makeItemIcon in ForestInventoryUI early-returns an ImageLabel with rbxthumb://type=Asset&id=<iconAsset> for any item that has iconAsset (covers shop + bag); vector emblems still used for items without one. See [[inventory_ui_icons]].

Lineup restructure (2026-07-03, later same day): reduced to 7 weapons, names now match the catalog models. Candy Cane REMOVED as a distinct weapon — the CandyCane id was repurposed into the free STARTER "Minecraft Sword" (id kept as "CandyCane" for save-compat; StarterWeapon unchanged). The ChocolateBlade id/tier was deleted (this is why there's a small early gap: Minecraft 12/free → Diamond Blade 22/750/r0 → Hammer 60/9000/r5). Final ladder: Minecraft Sword(starter,12) → Diamond Blade Sword(22,r0) → Hammer(60,r5) → Staff of Azure(95,r10) → Dark Matter Sword(150,r15) → Rainbow Star Sword(230,r20) → Azure Sword(360,r25). ServerStorage.Weapons tools renamed to match these names; old "Candy Cane" tool deleted. Wheel prize gear_lh label updated to "Diamond Blade Sword" (itemId still LollipopHammer). ANIMATION FIX: the catalog gear tools carried leftover objects (Animations folder, MouseInput RemoteFunction, Remote RemoteEvent, ThumbnailCamera, a stray Accessory) — Diamond Blade wasn't swinging. Stripped all non-{Handle, BasePart, ClientAttack, ShieldRig} children so every tool is uniform; swing is the shared Grip-tween in ClientAttack. NOTE: LoadAsset/insert_asset only works for these assets in Studio if the place is licensed — confirm in a published playtest.
