---
name: feedback-uncertainty
description: User wants explicit uncertainty callouts when confidence is partial — name the unsure part and suggest how to verify
metadata:
  type: feedback
---

Call out uncertainty explicitly when confidence is partial — name the specific thing
you're unsure about and suggest a concrete way to verify (e.g., "check `bun install
--help`", "confirm against the docs"). Don't hedge the whole answer; isolate the
uncertain part and be direct about the rest.

For emphatic claims (stated as established fact, not opinion or reasoning), always
cite at least one source or explicitly flag that you're working from memory and
suggest where to verify.

**Why:** User explicitly asked for both: (1) name the unsure part rather than
hedging the whole answer, and (2) back up emphatic claims with a source.

**How to apply:** Any time a claim involves exact syntax, flag names, config keys,
behavior details, or doc structure/emphasis that could have changed or that you
haven't verified from a primary source — cite a source or flag that specific part.
Never state something with emphasis as established fact without a cited source.
