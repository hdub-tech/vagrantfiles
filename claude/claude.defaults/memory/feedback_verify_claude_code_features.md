---
name: feedback-verify-claude-code-features
description: Before asserting what Claude Code (the CLI tool) can or can't do, verify against current docs or the claude-code-guide subagent — trained recall of Claude Code's own feature set has recency and salience gaps.
metadata:
  type: feedback
---

When answering a question about Claude Code's own capabilities — what
constructs exist (CLAUDE.md, `.claude/rules/`, skills, hooks,
subagents, MCP, etc.), whether a specific feature exists, how something
is configured — verify against current documentation (`code.claude.com`)
or delegate to the `claude-code-guide` subagent rather than answering
from trained recall alone.

**Why:** caught when asked to sort memory files into "memories vs.
rules, skills, etc." — `.claude/rules/` is a real, documented
construct, but wasn't salient enough in training to surface without an
explicit prompt to verify. Claude Code ships new features continuously,
and trained knowledge is frozen at a cutoff and weighted by how often a
topic appeared in training data, not stored as a lookup table — a
recent or narrowly-discussed feature (like a path-scoped rules
directory) can be entirely missing from recall even though it's fully
documented. The `claude-code-guide` subagent exists specifically to
close this gap by checking live docs, but wasn't used the first time
this came up.

**How to apply:**

- Any question shaped like "does/can Claude Code do X" or "what
  constructs/features exist for Y" — treat as a docs-verification
  question, not a recall question.
- Prefer routing to the `claude-code-guide` subagent, or a direct
  WebFetch/WebSearch against `code.claude.com`, before asserting
  completeness.
- Applies to the full feature surface: CLAUDE.md, `.claude/rules/`,
  skills, hooks, subagents, MCP servers, slash commands, settings — not
  just the two constructs that prompted this.

Related: [[feedback-official-docs-over-local-examples]] — same
"verify against the authoritative source, don't pattern-match from
what's already loaded" instinct, applied here to Claude Code's own
feature set rather than a file format spec.
