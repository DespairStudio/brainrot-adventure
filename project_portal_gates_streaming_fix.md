---
name: project-portal-gates-streaming-fix
description: Portal rebirth-gate bars reappeared after biome trips (StreamingEnabled); fixed with Persistent portals + re-apply listener 2026-07-08
metadata:
  type: project
---

Bug (fixed 2026-07-08): hub portal iron bars + "UNLOCKS AT REBIRTH X" sign came back after entering/exiting any biome, even for players meeting the requirement.

Cause: `StarterPlayerScripts.PortalGates` hides bars/rewrites signs **client-side only** (Transparency=1, CanCollide=false, sign text). `workspace.StreamingEnabled = true`, biomes are 2600–5250 studs away → hub streams out during a biome trip; on return the parts re-replicate with the **server's** saved state (visible bars, locked text) and nothing re-triggered `apply()` (no rebirth change, portal travel = PivotTo, no respawn).

Fix (two layers):
1. All 4 portal models in `workspace.TownHub.Portals` (Portal_PvPARENA / FOREST / DESERT / HELL) set to `ModelStreamingMode = Persistent` — they never stream out, so local overrides stick.
2. `PortalGates` got a safety net: debounced `workspace.DescendantAdded` listener re-runs `apply()` when parts named `Bar`/`BarH`/`Sign` (re-)enter the client world.

Verified in play test: rebirth 25 → bars hidden; forest round-trip (PivotTo -2625,20,0 and back) → still hidden, 0 colliding, sign "▶ ENTER"; rebirth 0 → 4/4 bars visible + locked text.

**GENERAL GOTCHA:** with StreamingEnabled, ANY client-side modification of world parts (transparency, text, color, position) is silently reverted when the part streams out and back in. Any future per-player world visuals need either Persistent models or a re-apply-on-stream-in listener. Related: [[project-town-hub-build]], [[project-map-shrink]].
