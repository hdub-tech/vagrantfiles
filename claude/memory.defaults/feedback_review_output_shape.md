---
name: feedback-review-output-shape
description: A code review is a read-only analysis task — never write a plan file or call ExitPlanMode for one; report What I verified → Findings → Verdict, then ask before showing "Not raised" or write actions needing consent
metadata:
  type: feedback
---

A code review is a read-only **analysis** task, not an implementation task.
Never write a plan file for one and never call `ExitPlanMode` — even when
plan mode is active.

**Why:** Explicitly requested by the user, after a `/review` of a PR ended
with a plan file plus an `ExitPlanMode` approval prompt. The user asked why
an approved review produced a plan. Two causes, worth keeping distinct:

1. Plan mode's workflow says the turn must end with `AskUserQuestion` or
   `ExitPlanMode`, but `ExitPlanMode`'s own description says to use it
   only when planning code that will be written, and NOT for research
   tasks (gathering information, searching, reading). The specific rule
   wins over the generic workflow preamble.
2. The review did surface real write actions (post the review, merge,
   open a follow-up issue, update a log) that require consent. Needing
   consent to act is NOT the same as needing to write a plan — that
   conflation is what produced the unwanted plan document.

**How to apply:**

- Read-only investigation is expected and encouraged — `gh pr view`,
  `gh pr diff`, `Read`, `grep`. A review cannot be written without
  fetching the diff. The prohibition is on plan files and `ExitPlanMode`,
  not on tool use.
- Initial response contains exactly these parts, in this order, then
  stops. They are headings, not numbered items, so that numbering stays
  reserved for findings:
  1. **What I verified** — checks actually run, with `file:line` sources;
     name the riskiest hypothesis and say whether it was ruled out.
  2. **Findings** — blockers first, then follow-ups, each severity-tagged.
  3. **Verdict** — approve / approve-with-followups / request-changes.
     Last, so evidence precedes the conclusion.
- Two sections are withheld from that initial response. After the
  verdict, iff either has content, ask which to show: (1) "Not raised",
  (2) "Write actions needing consent", (3) both. Offer only the options
  that have content. If the user picks 1 or 2, show it and then ask again
  for the remaining one. Never include either section inline unasked.
- **Not raised** covers suggestions considered and deliberately dropped,
  including any a prior review round rejected. Never re-litigate a
  suggestion the author already rejected with reasoning.
- Ask for go-ahead on write actions in one line, not a plan document.
- Cheapest prevention on the human's side: don't leave plan mode on for a
  review — it buys nothing on a read-only task and distorts the output.

Related: [[feedback-review-numbering]]
