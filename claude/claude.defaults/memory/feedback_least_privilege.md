---
name: feedback-least-privilege
description: Default to the least-privilege option in security-sensitive config (container capabilities, IAM, file perms, etc.); only escalate when a specific need is proven
metadata:
  type: feedback
---

Default to the least-privilege option in security-sensitive configuration
choices. Do not include broad-privilege settings (e.g. `privileged: true`,
wildcard IAM, 0777 perms, `--cap-add ALL`) unless a specific runtime
requirement forces it. If unsure, omit and add back as a targeted fix.

**Why:** User surfaced this while reviewing a molecule podman platform
config — I had `privileged: true` cargo-culted from older recipes even
though modern podman `systemd: always` handles systemd-in-container
without needing it. User confirmed least privilege is the default
preference across the board, not just for this one setting.

**How to apply:** When writing container configs, IAM policies, file
modes, sudoers entries, security contexts, or similar — start from the
minimum and add capabilities only when a concrete failure demands them.
Flag heavier-privilege settings in review even when they came from a
common recipe. In explanations, prefer phrasing like "omit unless you
hit X" over "include for compatibility."
