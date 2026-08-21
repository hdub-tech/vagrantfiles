---
name: feedback-skip-pointless-verification
description: "Don't run verification/validation steps that don't matter to the task or to me"
metadata:
  type: feedback
---

Don't run a verification step (e.g. JSON well-formedness check, re-reading a
file I just wrote) when the result doesn't actually matter to me or to the
task. For hand-authored data files like FINDINGS.json, the user does not care
whether it is strictly well-formed as long as I can make sense of it.

**Why:** The user values not burning tool calls / attention on checks with no
payoff. Honesty about outcomes is still expected — this is about skipping
pointless confirmation, not skipping mandated verification.

**How to apply:** Before running a validation/verification step, ask whether
its outcome changes anything I'll do next. If not, skip it. This does NOT
override CLAUDE.md's mandated verification steps for code changes
(lint → build → test), which remain required. See [[feedback-verification-order]].
