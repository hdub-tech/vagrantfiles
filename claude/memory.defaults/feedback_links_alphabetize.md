---
name: feedback-links-alphabetize
description: Rule for alphabetizing the <!-- Links --> section in markdown files
metadata:
  type: feedback
---

When alphabetizing entries in a `<!-- Links -->` section, ignore leading dots
(`.`) in the path. Sort on the first significant character.

**Why:** User corrected ordering of `.githooks/commit-msg` vs
`commitlint.config.js` — `c` < `g` so `commitlint` should come first, but a
naive sort puts `.` (ASCII 46) before `c` (ASCII 99), producing the wrong order.

**How to apply:** Strip any leading `./`, `../`, or `.` prefix before
comparing entries, then sort on the remaining string.
