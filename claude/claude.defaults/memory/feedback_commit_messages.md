---
name: feedback-commit-messages
description: Rules for suggesting commit messages — prefer brevity, always add Co-Authored-By when Claude assisted (model + version + email only, no context-window annotations), and validate every field against the repo's commitlint config before offering.
metadata:
  type: feedback
---

When suggesting a commit message, apply all three rules together:

1. **Prefer brevity / terseness.** Subject alone is often enough. Add a
   short body only when the diff doesn't explain the *why*. Length
   limits in commitlint (`subject-max-length`, `body-max-line-length`,
   `body-max-length`) are *ceilings*, not targets — do not fill them.

2. **Always include a `Co-Authored-By` trailer when Claude assisted**
   with the change (drafting, refining, or reviewing). Format:

   ```text
   Co-Authored-By: Claude <model> <version> <noreply@anthropic.com>
   ```

   Example: `Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>`.
   Exclude context-window annotations like `(1M context)` — just the
   model, version, and email.

3. **Design the message against the repo's commitlint config.** Before
   offering a suggestion, read `commitlint.config.{js,mjs,cjs,ts}` (or
   equivalent) and verify:
   - a. Type is in `type-enum` and meets `type-min-length`.
   - b. Scope is in `scope-enum` and meets `scope-min-length`.
   - c. Subject length ≤ `subject-max-length`.
   - d. Each body line length ≤ `body-max-line-length`.
   - e. Total body length ≤ `body-max-length`.

4. **Include an issue footer when the change addresses a known issue.**
   After the `Co-Authored-By` trailer, add **a blank line**, then one of:

   ```text
   Resolves #NN
   ```

   ```text
   Partially Resolves #NN
   ```

   Use `Resolves` when the commit fully closes the issue; use
   `Partially Resolves` when the commit only advances it. The blank line
   separating the `Co-Authored-By` trailer from the issue footer is
   required.

**Why:** User preference stated across multiple review-followup
exchanges. Ceilings-not-targets was called out explicitly ("just
because you CAN use 1000 chars… doesn't mean you should"). The
attribution rule mirrors the PR-template Co-Authored-By line but
strips context-window annotations that add noise.

**How to apply:**

- On every commit-message suggestion, whether asked or offered
  proactively.
- If the repo has no commitlint config, fall back to Conventional
  Commits defaults but keep the brevity and Co-Authored-By rules.
- Do NOT invent AI attribution if Claude didn't actually contribute
  to the change.
- The PR-template `Refined by @<git-user>` line is a PR-body trailer,
  not a commit trailer — do not fold it into individual commits.
