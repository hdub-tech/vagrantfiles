---
name: feedback-official-docs-over-local-examples
description: "When authoring something with a defined external spec (skill frontmatter, config formats, API usage), consult official documentation instead of copying conventions from existing local/repo examples."
metadata:
  type: feedback
---

When creating an artifact that follows an external, documented standard —
Claude Code skill `SKILL.md` frontmatter, a config file format, an API's
request shape, etc. — go to the official documentation for that standard
first, rather than pattern-matching off an existing example already in the
repo or on disk.

**Why:** caught while writing a new skill (`gh-ci-health-diff`). I had
started reading a sibling `SKILL.md` (`loom-devops-verify`) and a bundled
plugin skill to infer
frontmatter conventions before writing the new one. The user stopped me:
local examples may be stale, non-canonical, or just one author's style
choice — not the actual spec. Fetching the real Claude Code skills doc
turned up fields (`arguments`, `argument-hint`, `disable-model-invocation`,
the 1,536-char description+when_to_use cap, etc.) that wouldn't have
surfaced from imitation alone.

**How to apply:** before writing YAML frontmatter, a manifest schema, or
any other spec-governed file for the first time in a session, do a
WebFetch/WebSearch pass against the authoritative docs (Anthropic's for
Claude Code features, the tool/library's own docs otherwise) and treat
local examples as a secondary sanity check, not the primary source. This
generalizes beyond skills — same instinct applies to CLAUDE.md conventions,
CI workflow syntax, or any other place a local example might be
outdated or nonstandard.
