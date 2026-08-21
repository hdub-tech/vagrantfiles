---
name: feedback-repo-root-terminology
description: "\"Repo-root\" always means the non-worktree git root (the main working tree), not a `git worktree add`-created linked worktree."
metadata:
  type: feedback
---

When the user says **"repo-root"** they mean the **main working tree** —
the non-worktree git root. They do NOT mean any `git worktree add`-created
linked worktree under `worktrees/` (e.g. `worktrees/improve-readme/`),
even when work is currently happening in one.

**Why:** the user works in linked worktrees for parallel branch work
and needs an unambiguous term for the primary checkout.

**How to apply:** any path they describe as "repo-root/X" resolves to
the main checkout's `X`, never to a linked worktree's `X`. When
*I* need to refer to the same location back to them, use "main
checkout" or "main working tree" (git's own vocabulary for the primary
checkout as distinct from linked worktrees). Persistent artefacts —
plans, notes, docs — go in the main checkout so they survive
`git worktree remove`, not into a worktree that's ephemeral by design.

Related: [[project-worktree-workflow]] (if ever written — captures the
parallel-worktree development pattern this user uses).
