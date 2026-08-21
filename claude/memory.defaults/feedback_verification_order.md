---
name: feedback-verification-order
description: Verification steps in plans must follow the order lint → build → test
metadata:
  type: feedback
---

Always list verification steps in this order: lint before build before test.

**Why:** User corrected an out-of-order verification section (test was listed
before lint and build). The correct sequence is: lint → build → test.

**How to apply:** Any time a plan, checklist, or verification section includes
lint, build, and/or test steps, ensure they appear in that order. Applies to
both full triples and any subset (e.g., lint before test if build is absent).
