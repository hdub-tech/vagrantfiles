---
name: feedback-file-renames
description: File renames of tracked files must use `git mv`, and git commands require explicit human consent — so hand the rename to the user rather than doing it via plain `mv`.
metadata:
  type: feedback
---

When renaming (or moving) a file that is tracked in git, do NOT use plain `mv`.
Also do NOT run `git mv` yourself unless the user has explicitly authorized it.
Instead, describe the rename in the plan and hand it to the user to execute.

**Why:** The user's global `CLAUDE.md` says "Do NOT perform any git actions or
run any git commands without explicit human consent first." Renaming tracked
files is a git-shaping action even when done via plain `mv` — it leaves the
working tree in a "deleted + untracked" state that biases the eventual commit.
In an in-flight session on this collection, I used plain `mv` for two renames
and the user asked me to revert and let them do the `git mv` themselves.

**How to apply:**

- For any tracked file/dir rename: list the `git mv` commands in the plan
  under a "Hand-off to user" section; do NOT execute the move.
- For untracked files (e.g. scratch / gitignored / newly-created-this-session
  and not yet committed): plain `mv` is fine — no git side-effect.
- If uncertain whether a path is tracked, prefer asking or default to the
  hand-off approach.
