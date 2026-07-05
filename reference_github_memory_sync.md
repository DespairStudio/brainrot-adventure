---
name: reference-github-memory-sync
description: "github.com/MuTuOz/roblox-adventure holds a shared copy of project memory (used by the user's friend's Claude session too) — check it for updates not yet reflected here"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 3a91ef46-d312-430b-b4b2-db1cd36f29ad
---

The repo https://github.com/MuTuOz/roblox-adventure is a shared memory drop point between this project's collaborators (the user + friend, likely working from separate sessions/devices). Pulled from it on 2026-07-02 and found build docs for Biome 2 (desert, [[project_desert_biome_build]]), Biome 3 (hell, [[project_hell_biome_build]]), a treasure-chest world event ([[project_treasure_chest]]), and a status update that the Forest boss was removed ([[project_forest_boss_build]]) — none of which had made it into this memory store yet. The repo also has two full design-spec docs not mirrored into local memory: `Biome2_Desert_Design.md` and `Biome3_Hell_Design.md` (tuning tables, rationale).

The repo's own `MEMORY.md` index was stale (didn't list the newer files) — don't trust its index blindly, check the actual file list. Earlier files were uploaded via GitHub's web "Add file via upload," which mangled long filenames into Windows 8.3 short names (e.g. `PR20A6~1.MD`) — if pulling again and mangled names reappear, match by reading the `name:` field in each file's frontmatter rather than the filename.

Push access requires a GitHub PAT with **Contents: Read and write** — an earlier token only had read access (403 on push).

**2026-07-05 update:** two-way sync completed via git in the session sandbox (clone in a sandbox scratch dir — NOT the OneDrive folder, git file locking breaks there; token injected per-command, never stored in the remote). Pulled the collaborator's 07-02/07-05 uploads (biome gear, inventory/friends/robux, forest mob models, icons + town hub updates), then pushed: merged MEMORY.md index, [[project_trade_system]], the corrected forest-boss doc (REMOVED status — collaborator's copy predated the 2026-06-24 removal), and renamed all mangled 8.3 filenames to their canonical names so index links resolve.

**Why:** Establishes that this repo is the cross-session/cross-collaborator sync point, so future sessions should check it rather than assuming local memory is complete.
**How to apply:** When starting fresh work on this project, or when something in local memory looks stale, pull the repo and diff against local `.md` files before trusting either side.
