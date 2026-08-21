---
name: feedback-changelog-header-rule
description: "In CHANGELOG.md, a version number appears in a `##` header iff it has a git tag OR the body under that header explicitly names it — do not flesh out ranges with gap-fillers or invent details the body doesn't already discuss."
metadata:
  type: feedback
---

When rewriting version headers in `CHANGELOG.md`, the admission rule is
a disjunction:

> A version `X.Y.Z` appears in a `##` header iff **either** a `vX.Y.Z`
> git tag exists **or** the body under that header explicitly names
> `X.Y.Z`.

Bracket the version (making it a reference-style link) iff the tag
exists; leave it as plain text if it's referenced by the body but has
no tag yet (e.g. `1.7.11 - TBD`).

**Why:** the user has explicitly rejected fleshing out semver ranges
with versions that lack both a tag and a body reference:
*"I only want the header to contain numbers for tags or versions
that are explicitly referenced in the body (possibly missing tags).
do not flesh out a range if nothing to go with it"*.

**How to apply:**

- For every `##` version header in CHANGELOG.md, compute
  `{v : hasTag(v) ∨ bodyMentions(v)}` and enumerate those versions
  comma-separated, in **chronological (oldest → newest)** order
  inside the header — even though the outer file section order is
  newest → oldest — because the shared body typically reads as a
  chronological narrative and the header should match story order.
- Bracket a version iff its tag exists; leave TBD/un-tagged versions
  as plain text on the header line.
- Do NOT invent versions to fill semver gaps. If a version has no tag
  AND is not mentioned in the body, it simply doesn't appear.

Related: [[feedback-repo-root-terminology]] (main checkout vs. linked
worktree — the CHANGELOG may live in a linked worktree, but this rule
travels with the file, not the checkout).
