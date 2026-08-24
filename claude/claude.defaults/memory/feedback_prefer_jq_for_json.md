---
name: feedback-prefer-jq-for-json
description: When parsing/querying JSON from the shell, use jq if it's available and fits, rather than reaching for python -c/import json.
metadata:
  type: feedback
---

For quick JSON field extraction or filtering in a Bash command (e.g.
`podman info --format json | ...`, API responses, config dumps), check
`which jq` and use `jq` first. Only fall back to Python (or another
scripting language) when the transformation is too involved for a `jq`
one-liner (heavy custom logic, needs XML too, multi-step stateful
aggregation across a large document).

**Why:** caught reaching for `python3 -c 'import json...'` to pull one
field out of `podman info --format json` when `jq` was already installed
on the box. `jq` is the standard, lighter-weight tool for exactly this
job — spinning up a Python one-liner for a single field lookup is
unnecessary overhead and less idiomatic in a shell pipeline.

**How to apply:** before writing `python3 -c` or a python heredoc to
parse JSON output from a CLI tool, run (or recall) `which jq` — if
present, write the `jq` filter instead. This is about picking the right
tool for simple JSON queries, not a blanket ban on Python — XML parsing,
multi-file aggregation, or genuinely complex logic still warrant Python
(e.g. the JUnit XML analysis in this session used Python because it
needed `xml.etree`, which jq can't do).
