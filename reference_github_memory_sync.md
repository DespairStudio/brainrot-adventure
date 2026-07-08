---
name: reference-github-memory-sync
description: "github.com/DespairStudio/brainrot-adventure holds a shared copy of project memory (used by the user's friend's Claude session too) — check it for updates not yet reflected here. (Moved 2026-07-07 from the old repo github.com/MuTuOz/roblox-adventure.)"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 3a91ef46-d312-430b-b4b2-db1cd36f29ad
---

The repo https://github.com/DespairStudio/brainrot-adventure is the shared memory drop point between this project's collaborators (the user + friend, likely working from separate sessions/devices). **The repo moved here on 2026-07-07** — the old location was github.com/MuTuOz/roblox-adventure (dead; use the new one). Access uses a fine-grained PAT supplied by the user per session; the token is NOT stored in memory (secret) — ask the user for a current one when a pull/push is needed. Pulled from the old repo on 2026-07-02 and found build docs for Biome 2 (desert, [[project_desert_biome_build]]), Biome 3 (hell, [[project_hell_biome_build]]), a treasure-chest world event ([[project_treasure_chest]]), and a status update that the Forest boss was removed ([[project_forest_boss_build]]) — none of which had made it into this memory store yet. The repo also has two full design-spec docs not mirrored into local memory: `Biome2_Desert_Design.md` and `Biome3_Hell_Design.md` (tuning tables, rationale).

The repo's own `MEMORY.md` index was stale (didn't list the newer files) — don't trust its index blindly, check the actual file list. Earlier files were uploaded via GitHub's web "Add file via upload," which mangled long filenames into Windows 8.3 short names (e.g. `PR20A6~1.MD`) — if pulling again and mangled names reappear, match by reading the `name:` field in each file's frontmatter rather than the filename.

Push access requires a GitHub PAT with **Contents: Read and write** — an earlier token only had read access (403 on push).

**2026-07-05 update (commit 731c550):** two-way sync completed via git in the session sandbox (clone in a sandbox scratch dir — NOT the OneDrive folder, git file locking breaks there; token injected per-command, never stored in the remote). Pulled the collaborator's 07-02/07-05 uploads (biome gear, inventory/friends/robux, forest mob models, icons + town hub updates), then pushed: merged MEMORY.md index, [[project_trade_system]], the corrected forest-boss doc (REMOVED status — collaborator's copy predated the 2026-06-24 removal), renamed all mangled 8.3 filenames to canonical names, and removed three frontmatter-stripped case-duplicate files (PROJECT_HANDOFF.md, Game_Balance_Fixes.md, Game_Balance_Theorycraft.md — full versions live at the lowercase names). Repo is now fully normalized: every file at its canonical name, all index links resolve.

**2026-07-07 repo migration:** collaborator moved the shared memory repo to **github.com/DespairStudio/brainrot-adventure** (new fine-grained PAT provided). Cloned + pulled: repo holds the same memory files + the two design-spec docs (`Biome2_Desert_Design.md`, `Biome3_Hell_Design.md`); its copies are as-of the 07-05 sync (CRLF, minus this session's post-07-05 refinements) — **no new collaborator content beyond what local memory already has**. Did NOT push, per request. All local memory URL references updated old→new repo.

**2026-07-07 pushes (commits 8c58781, e4f558f):** pushed [[project_trophy_offline_build]] + updated MEMORY.md index (trophy line), then the same file again after the CANDY SHOP trophy row was added. Repo state before = same day's wheel-restyle commit (0a55c47); rest already in sync.

**2026-07-08 issue-fix push:** collaborator filed GitHub **issue #1 "Golden trophy is floating"** (cup poking out of the base dome after shop purchase) — same bug fixed in Studio that day (plinth-cap axis; see [[project_trophy_offline_build]]). Pushed the updated trophy doc with "Fixes #1" to auto-close. **Repo Issues are now a bug-report channel from the collaborator — check open issues on every pull.** Note: sandbox proxy blocks api.github.com (403), but public issue pages are readable via plain web fetch; git over github.com works fine.

**Why:** Establishes that this repo is the cross-session/cross-collaborator sync point, so future sessions should check it rather than assuming local memory is complete.
**How to apply:** When starting fresh work on this project, or when something in local memory looks stale, pull the repo and diff against local `.md` files before trusting either side.
