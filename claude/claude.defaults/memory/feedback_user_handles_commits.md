---
name: feedback-user-handles-commits
description: user always handles commits/mutating git commands themselves — never commit, and never ask "shall I commit?"
metadata:
  type: feedback
---

The human always runs mutating git commands themselves. Said explicitly
twice: "i will always handle commits", and "only the human does git edit
commands" after a finished task was closed with "Shall I commit the four
files?".

**Why:** stated as a standing workflow preference, not a one-off answer
about a specific change. It reinforces (and narrows) the general rule
of never running git commands without explicit consent — the human is
saying that consent will just never come, so don't bother asking each
time. Asking anyway reads as not having listened, since it has now been
said more than once.

**How to apply:** after making file edits, end the turn by stating
"changes made, not committed" plus the list of changed/untracked paths,
and stop there. Do NOT end with "want me to commit this?", "shall I
commit?", or any variant — not even as a one-line offer, and not even
when the work is fully verified and obviously ready to land. Committing,
branching, pushing, and PR creation are all the human's actions, in any
repo. Read-only git inspection (`status`, `diff`, `log`) has been
tolerated in practice, but the general rule is broader than commits, so
prefer non-git tools (Read/Grep) where they answer the question.
Related: [[feedback-file-renames]] (hand `git mv` over rather than
running it), [[feedback-commit-messages]] (how to shape the message text
the human will use).
