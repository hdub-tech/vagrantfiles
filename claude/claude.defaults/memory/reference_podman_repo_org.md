---
name: reference-podman-repo-org
description: Podman's GitHub repos moved from the containers/ org to podman-container-tools/ — use the new org for gh searches/API calls.
metadata:
  type: reference
---

Podman-related repos have moved from the `containers/` GitHub org to
`podman-container-tools/`. Confirmed via `gh api repos/podman-container-tools/podman`
(exists, not archived) vs. `containers/podman` (stale/moved).

Known moved repos, confirmed:
- `containers/podman` → `podman-container-tools/podman`
- `containers/buildah` → `podman-container-tools/buildah`
- `containers/skopeo` → `podman-container-tools/skopeo`
- `containers/image`, `containers/storage`, and `containers/common` were
  **consolidated into one monorepo**: `podman-container-tools/container-libs`
  (these three are no longer separate repos at all, moved or otherwise —
  search/query `container-libs` for issues/code that used to live in any
  of the three).

**How to apply:** when searching or querying Podman/Buildah/Skopeo
issues, PRs, or code via `gh`, use the `podman-container-tools/` org,
not `containers/`. For anything that used to be in `containers/image`,
`containers/storage`, or `containers/common`, query
`podman-container-tools/container-libs` instead. Any other `containers/`
org repo not listed above hasn't been confirmed either way — verify with
`gh api repos/<org>/<repo>` before assuming.
