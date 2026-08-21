# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository. <!-- markdownlint-disable-line MD013 -->

# Linting

Run from `$project_dir`:

```bash
podman run --rm -v .:/workdir docker.io/davidanson/markdownlint-cli2:v0.22.1
```

Configured via `$project_dir/.markdownlint-cli2.yaml` (scans `**/*.md*`,
respects `.gitignore`) with custom rules in `$project_dir/.markdownlint.yaml`:

- MD022 (`lines_above: 0`): blank lines above headings are not required
- MD025 disabled: multiple H1s per file are allowed
- MD033: inline HTML allowed only for `details`, `br`, `ul`, `li`, `sup`, `a`

> [!WARNING]
> Do NOT edit `.markdownlint.yaml` or `.markdownlint-cli2.yaml`, and do NOT
> add `markdownlint-disable` directives to any markdown file, without explicit
> human confirmation first.

Keep `.markdownlint.yaml` rule headers (`MD001`, `MD013`, etc.) in numerical
order. Keep keys within each rule block alphabetized.

Before adding a configuration key to `.markdownlint.yaml`, verify it exists
in the spec URL cited in that rule's comment header.

# Code Style

Whenever a value is used more than once, extract it into a named variable.

Prefer reusing existing variables over creating new ones with identical content.
If a new variable must duplicate an existing value (e.g., bridging between two
language contexts), explain the reason in a comment.

# Communication Style

Do not preface statements with phrases that assert truthfulness — "honest",
"honestly", "to be honest", "frankly", "to be frank", "truly", "genuinely",
and similar. They add no information; honesty is the default. Say the thing
directly. Also avoid constructions that frame a specific section as requiring
special candor ('warrants honest analysis', 'to be clear', 'in fairness') as
these carry the same implication.

# Markdown Style

Wrap all markdown prose at 80 characters per line (hard wrap). Code blocks and
tables are exempt. For list items that exceed 80 characters, break the line and
indent the continuation by 2 spaces so it aligns with the list text.

All markdown files that reference anchors, relative files or external URLs
must collect those links in a `<!-- Links -->` section at the very end of
the file. Within that section, group links in the order below with no
blank lines between groups, and alphabetize within each group. Do not add
header labels between groups.

1. Anchored in-file links (destination starts with `#`)
2. Relative file links (destination starts with `./` or `../`; always
   use the explicit `./` or `../` prefix)
3. Public URLs (destination starts with `https://`)

# Architecture

Two Vagrant VM templates, each in its own subdirectory:

| Template | Base Box | Config Location |
| --- | --- | --- |
| `kali/` | kalilinux/rolling | `$project_dir/kali/defaults.yml` + optional `kali/overrides.yml` |
| `python/` | debian/bookworm64 | Variables block at top of `$project_dir/python/Vagrantfile` |
| `claude/` | bento/ubuntu-24.04 | Variables block at top of `$project_dir/claude/Vagrantfile` + optional `claude/claude.overrides/` |

## Key Patterns

**Defaults + Overrides**: `$project_dir/kali/defaults.yml` is the source of
truth for Kali config. To customize, copy it to
`$project_dir/kali/overrides.yml` and edit only that file. Never modify
`defaults.yml` directly.

**Self-contained Vagrantfiles**: All provisioning uses inline shell provisioners
— no Ansible, no File Provisioner. This is intentional.

**Idempotent provisioners**: Every provisioner guards with an existence check
(e.g., `if [ ! -d ~/.pyenv ]`), making `vagrant up --provision` safe to re-run
without side effects.

**`run_as_root` flag** (kali only): Boolean in `defaults.yml` controlling
whether shell provisioners run as the root or vagrant user. The apt-install and
chsh provisioners always run as root regardless of this setting.

**Squiggly heredocs for embedded scripts**:

- `custom_setup_script` (kali): unquoted (`<<~SCRIPT`) — Ruby interpolation
  enabled; reference Vagrantfile variables with `#{variable}`
- `project_setup_script` (python): quoted (`<<~'SCRIPT'`) — no interpolation;
  script runs exactly as written

**Checksum verification**: `compressed_app_urls` entries accept optional
`algorithm` and `checksum` keys (any algorithm supported by `cksum`).

**Variable expansion in shortcuts and scripts**: `$apps_dir` and
`$git_repos_dir` are expanded inside `.desktop` shortcut files and within
`pipx_packages` / `custom_setup_script`.

## Key Files

- `$project_dir/kali/defaults.yml` — all Kali defaults; source of truth,
  never edit directly
- `$project_dir/kali/Vagrantfile` — 16-step Kali provisioning sequence
- `$project_dir/python/Vagrantfile` — 8-step Python provisioning sequence
- `$project_dir/.github/pull_request_template.md` — component-by-component
  testing checklist required before merge

# Verification Steps

Verification steps in a plan are mandatory unless a human explicitly says
otherwise. Plans are not complete until verification steps have been run —
"edits look correct" is not the same as plan completion.

- Always write verification steps as checkboxes (`[ ]`) so they are harder to skip.
- Always explicitly announce "Now running verification steps" before executing them.
- Whenever a Vagrantfile is modified on a branch, the version number should be
  increased by 1. If it has already been increased once on this branch, it does
  not need to be done again.
- If a Vagrantfile was modified, the Changelog section of the corresponding
  README should be updated to include all the changes in the recent version.
- Check off each step (`[x]`) as it passes.
- Do not declare a task done until all verification checkboxes are checked.
- Any update to the plan resets all verification checkboxes to unchecked
  (`[ ]`), unless a human explicitly says not to.
- When announcing a plan change, remind the human that it will reset the
  Verification steps unless they choose otherwise.

# Commit Messages

Commit messages should follow the following guidelines:

```ebnf
<commit-message> ::= <type> "(" <scope> ")" ":" " " <short-summary>
            [ <CRLF> <body> ]
            [ <CRLF> <important-footer> ]
            [ <CRLF> <issue-footer> ]

<type> ::= "build" | "ci" | "docs" | "feat" | "fix" | "perf" | "refactor" | "revert" | "style" | "test"
<scope> ::= "claude-vm" | "hashicorp-vagrant-vm" | "kali-vm" | "podman-vm" | "python-vm" | "claude-ai" | "commitlint" | "common" | "markdownlint" | "shellcheck"
<body> ::= [ 1*80<character> [ <CRLF> <body> ] ]
<important-footer> ::= <important-token> ":" " " <short-summary>
            [ <CRLF> <body> ]
<short-summary> ::= 1*80( lowercase-line )
<important-token> ::= "BREAKING CHANGE" | "DEPRECATED"
<issue-footer> ::= <issue-token> " " "#" 1*( digit )
<issue-token> ::= "Closes" | "Fixes" | "Resolves"
<lowercase-line> ::= <any lower case letter and comma and colon>
<character> ::= <any ASCII character except CR, LF, or null>
<CRLF> ::= <carriage return> <line feed>
<digit> ::= "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
```

TL:DR: `type(scope): short-summary` at a min, with:

- `type` **AND** `scope` **AND** `short-summary` are **REQUIRED**
- `body` is **OPTIONAL**
- `important-footer` is **REQUIRED IF** there is a breaking change or something
  is deprecated and then **MUST** include migration or update recommendations
- `issue-footer` is **REQUIRED IF** there is an accompanying ticket

# Testing / PR Requirements

- Test `vagrant up` with defaults and again with a populated `overrides.yml`
- Test idempotence: re-run `vagrant up --provision` and verify no failures
  or unintended changes
- Work through the checklist in
  `$project_dir/.github/pull_request_template.md` item by item before opening
  a PR
