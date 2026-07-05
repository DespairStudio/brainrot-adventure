---
name: project_trade_system
description: "Player-to-player gear trading: hold-E 'Trade Up' prompt, rebirth-gap ±5 constraint, two-sided window, atomic swap; built 2026-07-05"
metadata: 
  node_type: memory
  type: project
---

Player trading built 2026-07-05. Design decisions (user-confirmed): max rebirth gap **±5**; tradeables = **BAG weapons+shields only** (no gold/crystals/cosmetics/chest items); high-tier gear = **own-now-equip-later** (trade goes through, equip gated by reqRebirth).

**Flow:** every character gets a server ProximityPrompt "Trade Up" on HumanoidRootPart (E, HoldDuration 2, dist 9, LoS off, ObjectText=DisplayName) → hold-E fires request → target gets accept/decline popup (20s timeout) → accept opens two-sided trade window → both press READY (any offer change resets both) → 3s lock countdown → atomic swap.

**Config:** `GameData.Trade` = {MaxRebirthGap=5, HoldSeconds=2, PromptDistance=9, RequestTimeout=20, LockSeconds=3, MaxOfferSlots=4, MaxDistance=30}.

**Server `ServerScriptService.TradeService`** (new Script): creates remotes TradeRequest/TradeState/TradeAction in ReplicatedStorage.Remotes at runtime. Gap checked at request, at accept, AND at executeSwap. Dupe rule: receiver must not `Profile.OwnsItem` the offered id (set-based inventory — also auto-blocks starters since everyone owns them). Offers validated bag-only (`p.weapons/p.shields`, chest excluded). Cancel paths: X button, decline, request expiry sweeper (2s loop), player leaves, distance >30 (1s watchdog), any failed revalidation at swap. Spam guard 3s/request. Prompts disabled (server) while in a trade. executeSwap: revalidate all → `Profile.TakeItem` giver / `Profile.GrantItem` receiver → if equipped weapon left, in-hand Tool rebuilt (giveWeapon-pattern local copy w/ WEAPON_NAMES clear) → `Profile.Save` both immediately.

**ProfileService additions:** `M.TakeItem(plr,id)` → ok, weaponChanged (removes bag item; equipped falls back to StarterWeapon/StarterShield). `M.EquipWeapon/EquipShield` now return ok,reason and **gate on reqRebirth** (own-now-equip-later); PlayerInit's EquipItem handler toasts the reason on failure. BAG gear slots (ForestInventoryUI gearSlot) show an amber "REB n" badge when `data.reqRebirth > plr:GetAttribute("Rebirth")`.

**Client `StarterPlayer.StarterPlayerScripts.TradeUI`** (new LocalScript): mutes OWN TradePrompt locally (MaxActivationDistance=0 local write — server copy unaffected for others). Popup (380x128 top-center): name + rebirth, ACCEPT/DECLINE, shrinking timeout bar, popGen counter kills stale timers. Trade window (720x470 center, Grow-a-Garden style: chocolate bg, black borders, FredokaOne+text strokes): YOUR OFFER / <NAME>'S OFFER grids (2x2, 156x62 cards), gear picker scroll (own bag minus already-offered, iterates WeaponOrder+ShieldOrder), READY!/WAIT.../LOCKED button states, partner-ready label, "Trading in n..." countdown label. Item cards: rarity-tinted darkenC(0.42) + rbxthumb iconAsset (weapons) or vector crest (shields). Inventory known via own InventorySync listener. TradeState msgs: full state | {countdown=n} | {status="done"/"cancelled"} closes.

**Quirks/notes:** weapon upgrade levels (`p.upgrades`) do NOT transfer — they're per-player and silently persist if the weapon ever comes back. Trade toasts reuse Remotes.Toast. Deposit-to-chest mid-trade is handled (item missing at swap → clean cancel).

**Verified (solo playtest):** compiles clean; remotes + prompt props confirmed server-side; own-prompt muted (dist 0); window + popup render paths exercised via injected TradeState/TradeRequest — offers, icons, picker exclusion, ready states, texts all correct. **OPEN: full 2-player flow untested** (Play Solo = 1 player) — test with the collaborator: request→accept→swap, gap-block toast (needs reb diff >5), equipped-weapon trade (tool should swap to starter in hand), REB badge + blocked-equip toast on traded-up gear.

**Gotchas:** (1) execute_luau `require()` gets a FRESH module instance (empty `profiles`) — can't inspect live ProfileService state from the tool; verify via attributes/instances instead. (2) A `.` vs `..` concat typo in ProfileService broke EVERY server script at require-time — console showed "Expected identifier" + cascade of "Requested module experienced an error while loading". (3) screen_capture still times out in play mode (same as forest-mob session).

See [[project_rebirth_shop_build]] (BuyItem/equip), [[project_balance_system]] (gear bands — gap 5 matches tier cadence), [[project_chest_steal]] (GrantItem precedent), [[project_inventory_friends_robux]] (social layer).
