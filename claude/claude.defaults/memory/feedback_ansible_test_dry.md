---
name: feedback-ansible-test-dry
description: For ansible molecule tests, extract role-internal constants (fail_msg text, computed paths) into vars/main.yml and load them in tests via a task-list helper — do not duplicate into test-side fixtures.
metadata:
  type: feedback
---

For ansible role molecule tests, when a test assertion depends on a
role-internal string (an assert `fail_msg`, a computed path derived
from role defaults, etc.), the constant belongs in `vars/main.yml`
under a `__<role_name>_*` name, and the test should reference it by
loading the role's vars via a small task-list helper (e.g. an
`import_tasks` of a `load_role_defaults.yml` that does `include_vars`
on the role's `vars/main.yml` and `defaults/main.yml`).

Do NOT duplicate the constant into test-side fixtures — molecule
`inventory/group_vars/`, `provisioner.inventory.group_vars` inline,
scenario-local `vars.yml`, or hardcoded strings inside the test's
assertion clause.

**Why:** The user explicitly chose DRY-with-role-vars over eliminating
a small helper file when the alternative was to duplicate an assert
message between the role's `fail_msg` and the test's rescue assertion.
Their words: *"we do not break DRY"*. Duplication drifts silently as
the role evolves — a rename or reword of the role's constant leaves
the test assertion still matching the *old* string, so the test keeps
passing against outdated expectations. A shared import guarantees the
test asserts against the exact string the role uses at runtime.

**How to apply:**

- Role side: put the constant in `roles/<name>/vars/main.yml` under a
  `__<role_name>_<purpose>` name (double-underscore signals role-private
  per the convention already in use).
- Role code: reference the var directly in `fail_msg` (or wherever
  the constant is emitted).
- Test side: keep (or add) a small `molecule/tasks/load_role_defaults.yml`
  helper that `include_vars` the role's `vars/main.yml` (and `defaults/main.yml`
  if the test also needs public defaults). Import it in the test's
  `pre_tasks` with `ansible.builtin.import_tasks`.
- Test assertion: reference the same var by name. Sync is guaranteed
  because both sides pull from `vars/main.yml`.
- The helper's cost (one import + a few lines of `pre_tasks`) is
  trivial vs. the drift risk of duplication.
- Related: [[feedback-least-privilege]] — same "prefer principled
  small file over shortcut duplication" instinct.
