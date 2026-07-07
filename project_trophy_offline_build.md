---
name: project-trophy-offline-build
description: Offline earnings + Robux GOLDEN TROPHY (x3) — built 2026-07-07; replaces old boss-trophy income
metadata: 
  node_type: memory
  type: project
  originSessionId: 3b70366f-4ffa-4130-86e0-ae6e9ebaa713
---

# Offline Earnings + GOLDEN TROPHY (built & playtested 2026-07-07)

User-approved design: EVERYONE banks gold while logged out; the Robux GOLDEN TROPHY multiplies it ×3. Rate = 100 + 50×rebirth gold/hour, cap 12h. Price 199 R$ (Developer Product, same pattern as cosmetics/steal).

## What replaced what
The old boss-trophy passive income (5 gold/min, collect-at-base prompt, `bossTrophy`-gated) was REMOVED — it was unreachable since the boss pivot. `ServerScriptService.TrophyIncome` deleted. `bossTrophy` profile field + BaseRegistry.SetBaseTrophy (Warden bust display) kept intact for future hub-arena bosses; no income tie anymore. `trophyLastCollect` field dropped from profiles.

## Server
- **GameData**: `GameData.Offline = {BaseGoldPerHour=100, GoldPerRebirth=50, CapHours=12, TrophyMult=3}`, `GameData.OfflineGoldPerHour(rebirth)`, `GameData.TrophyProductId=0` (PLACEHOLDER — create 199 R$ Dev Product on Creator Dashboard and paste id), `GameData.TrophyPriceRobux=199`. Old TrophyIncomePerMin/TrophyMaxBank removed.
- **ProfileService**: profile += `goldenTrophy` (bool), `lastSeen` (os.time stamped fresh in every Save). In `Load`, if saved `lastSeen>0`: earned = floor(clamp(hoursAway,0,12) × OfflineGoldPerHour(rebirth) × (goldenTrophy and 3 or 1)), added to gold, report stashed in local `offlineReports`. `GrantGoldenTrophy(plr)` (sets `GoldenTrophy` player attribute), `GetGoldenTrophy`, `TakeOfflineReport` (one-shot). Sync payload += `goldenTrophy`, `offline={perHour,capHours,mult,price}` (removed trophyPending/trophyRatePerMin — no client used them).
- **MonetizationService**: ProcessReceipt route for TrophyProductId → GrantGoldenTrophy + Save + gold toast. Studio-only `Remotes.RequestTrophy` free grant (RequestSteal pattern). `Remotes.OfflineEarnings` handshake: client fires → if profile loaded, server replies with report or `{none=true}`; client retries every 2s (up to 10×) until any reply.
- **ServerScriptService.GoldenTrophy** (new Script): builds/destroys a golden cup Model ("GoldenTrophyCup") on the owner's base `Yard.PlinthCap` when player attribute `GoldenTrophy` is true — Foot/Stem cylinders + Ball bowl + spinning Neon rim + PointLight + sparkle ParticleEmitter; sets TrophyTagAnchor billboard label "GOLDEN TROPHY - x3 offline $$" (empty when inactive). Refresh: OwnerUserId attr, per-player GoldenTrophy attr, 3s-delayed init.

## Client
- **StarterPlayerScripts.WelcomeBackUI** (new LocalScript): on join handshake → popup "WELCOME BACK! +$X, earned while you were away (Xh Ym)". Non-owners: "With the GOLDEN TROPHY you'd have +$3X!" + GET button (PromptProductPurchase, or RequestTrophy free in Studio). Owners: "GOLDEN TROPHY x3 ACTIVE" badge. FredokaOne/dark/gold style, Back-easing pop tween. No popup when earned = 0 or first-ever session.
- Remotes added: `OfflineEarnings`, `RequestTrophy`.

## Verified in playtest (2026-07-07)
Grant flow (attr + cup on Base_1 plinth + billboard label), both popup variants (screenshots), formula check (reb 20 → 1100/h → 12h×3 = 39600). Cup upsized ~1.4× after first screenshot looked small inside dome.

## Open / caveats
- **DataStore API blocked in this Studio session** (StudioAccessToApisNotAllowed) → the real save→rejoin offline grant is UNTESTED end to end; works only where DataStore is available (published game). Old saves lack `lastSeen`, so the FIRST login after this update earns nothing — normal from the second login.
- TrophyProductId still 0; purchases are Studio-free-grant only until a real product id is pasted.
- Purchase surface = welcome-back popup only; a shop-panel trophy row could be added later.

Related: [[project_rebirth_shop_build]] [[project_hub_systems]] [[game_balance_theorycraft]]
