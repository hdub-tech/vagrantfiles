---
name: feedback-review-numbering
description: In /review and /security-review output, use continuous numbering (1., 2., 3., …) across the entire review with letters (a., b., c.) for sub-points, so items can be referenced by number in follow-up.
metadata:
  type: feedback
---

In `/review` and `/security-review` output, number every feedback item
with a single continuous sequence (`1.`, `2.`, `3.`, …) that runs across
the *entire* review — do NOT restart numbering at each section. Sub-points
under a numbered finding use letters (`a.`, `b.`, `c.`), not bullets.

**Why:** Not explicitly stated by user. Inferred: continuous numbering
gives every finding a unique, stable identifier that can be referenced in
follow-up ("re: #7, I disagree because…") without ambiguity across
sections. Restarting numbering per section would produce collisions (two
`#3`s), which defeats the purpose. Do not over-generalize this to other
output types without confirmation.

**How to apply:**

- Applies to the review body — every finding, suggestion, or observation
  gets its own number in one continuous sequence.
- Section headers (Correctness, Style, Security, …) are still allowed and
  useful; they group the numbered items but do not reset the counter.
- Sub-points under item N use `a.`, `b.`, `c.` — never bullets or dashes.
- Does NOT apply to non-review content in the same response (e.g. a
  suggested test checklist, which stays as `- [ ]` checkboxes).
- Does NOT apply to other slash commands or general responses unless the
  user extends this rule.
