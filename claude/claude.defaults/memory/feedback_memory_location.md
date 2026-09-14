---
name: feedback-memory-location
description: Built-in auto-memory is per-project only (~/.claude/projects/<project>/memory/); ~/.claude/memory/ is a hand-maintained cross-project tier pulled in via a ~/.claude/CLAUDE.md instruction, with writes backed up to the vagrantfiles project's claude.defaults/memory/ if that project is on disk
metadata:
  type: feedback
---

The built-in auto-memory system only auto-loads one location, scoped
to the current project: `~/.claude/projects/<escaped-cwd>/memory/`.
There is no built-in cross-project tier, and no project-level
`<repo>/.claude/memory/` auto-load mechanism.

Cross-project memory lives at `~/.claude/memory/`, pulled in by an
explicit instruction in `~/.claude/CLAUDE.md` (see its Memory section)
rather than by the auto-memory system itself. Its `MEMORY.md` is
authoritative for cross-project memory; the per-project `MEMORY.md`
under `~/.claude/projects/.../memory/` is authoritative for that
project only. Do not merge the two indexes together.

**Why:** the user wants scope-specific memory (a project can hold
rules that don't leak into unrelated projects) plus a place for
genuinely universal rules (commit message format, review shape, etc.)
that shouldn't have to be re-learned per project. Since the built-in
mechanism doesn't support a cross-project tier natively, `~/.claude/CLAUDE.md`
is what bridges the gap. User-level memories are also seeded into
fresh VMs from a tracked defaults directory in the vagrantfiles
project — so new cross-project memories need to be back-propagated
there to survive VM rebuilds.

**How to apply:**

- Reads: `~/.claude/projects/<project>/memory/MEMORY.md` loads
  automatically for the current project; `~/.claude/memory/MEMORY.md`
  loads because `~/.claude/CLAUDE.md` says to read it. Treat both as
  in effect at once — there's no collision-priority logic needed since
  they're separate scopes, not shadowing tiers.
- Writes:
  - Project-specific memory (only relevant to the repo at hand): write
    to `~/.claude/projects/<project>/memory/`.
  - Cross-project memory (applies regardless of repo): write to
    `~/.claude/memory/`. **Also**, if the vagrantfiles project (the
    repo that seeded this VM's cross-project memory defaults,
    identifiable by its `claude/claude.defaults/memory/` and
    `claude/Vagrantfile`) can be located on disk, back up the same
    memory file into that project's `claude/claude.defaults/memory/`
    directory AND update that project's `MEMORY.md` index — so the
    memory survives VM rebuilds and propagates to future VMs
    provisioned from the same source. Tell the human so they know to
    `git add` and commit those files.
