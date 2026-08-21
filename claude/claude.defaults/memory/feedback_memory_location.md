---
name: feedback-memory-location
description: Auto-memory read priority is project → user (higher wins on collision); user-level writes should also be backed up to the vagrantfiles project's claude.defaults/memory/ if that project is on disk
metadata:
  type: feedback
---

Auto-memory lives in two candidate locations. When loading context,
check both in priority order — on filename collision, the
higher-priority copy wins:

1. `<repo>/.claude/memory/` — project-scoped, may be git-tracked.
   Resolved from the current working directory; a
   `.githooks/post-checkout` hook (when present in the repo) symlinks
   `<linked-worktree>/.claude/memory` → `<main-worktree>/.claude/memory`
   so any worktree resolves to the same directory transparently — no
   `git worktree list` gymnastics needed.
2. `~/.claude/memory/` — user-global fallback (least specific).

Each location's `MEMORY.md` is authoritative for its own scope — do
NOT merge indexes across locations into one file.

**Why:** the user wants scope-specific memory (a project can shadow a
global rule without deleting it) so a rule that only applies in one
repo does not leak into unrelated projects. User-level memories are
also seeded into fresh VMs from a tracked defaults directory in the
vagrantfiles project — so new user-level memories need to be
back-propagated there to survive VM rebuilds.

**How to apply:**

- Reads: load whichever of the two `MEMORY.md` files exist; resolve
  collisions by priority (project wins over user).
- Writes:
  - When the memory is clearly project-specific AND should be committed
    with the current repo, write to `<repo>/.claude/memory/` and tell
    the human so they know to `git add` it.
  - Otherwise write to `~/.claude/memory/` (user scope). **Also**, if
    the vagrantfiles project (the repo that seeded this VM's user-level
    memory defaults, identifiable by its `claude/claude.defaults/memory/` and
    `claude/Vagrantfile`) can be located on disk, back up the same
    memory file into that project's `claude/claude.defaults/memory/` directory
    AND update that project's `MEMORY.md` index — so the memory
    survives VM rebuilds and propagates to future VMs provisioned from
    the same source. Tell the human so they know to `git add` and
    commit those files.
- Never write into the auto-memory system's cache directory
  (`~/.claude/projects/…/memory/`) — that path is a machine-local
  cache, not a persistent memory location.
