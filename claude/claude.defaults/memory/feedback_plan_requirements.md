---
name: feedback-plan-requirements
description: Tests and docs are always required components of implementation plans
metadata:
  type: feedback
---

Tests and docs are always required in implementation plans — never omit them.

**Why:** Explicitly requested by the user. Plans without tests and docs are
incomplete by definition, regardless of scope or complexity.

**How to apply:** Every implementation plan must include:
1. A tests section with specific test cases (file path, describe/it names,
   mocking strategy)
2. A docs section identifying which files need updating (CLAUDE.md architecture
   map, JSDoc, README if user-facing)
3. Both sections appear even for small changes — "no tests needed" or "no docs
   needed" requires explicit justification, not silence

Related: [[feedback-verification-steps]]
