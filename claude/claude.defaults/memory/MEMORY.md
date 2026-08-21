# Memory Index

- [Memory location](feedback_memory_location.md) — read priority: project → user; user-level writes also back up to the vagrantfiles project's `claude.defaults/memory/` (if the project can be found on disk) so memories survive VM rebuilds
- [Links section alphabetization](feedback_links_alphabetize.md) — ignore leading dots when sorting `<!-- Links -->` entries
- [Junior dev upskilling sources](feedback_junior_dev_upskilling_sources.md) — always include an official doc link (MDN/Node/npm/OWASP/etc.) when pitching feedback at junior level
- [Uncertainty callouts](feedback_uncertainty.md) — name the unsure part explicitly and suggest how to verify; don't hedge the whole answer
- [Verification step order](feedback_verification_order.md) — lint → build → test, always in that order
- [Skip pointless verification](feedback_skip_pointless_verification.md) — don't run checks whose outcome doesn't matter (e.g. JSON well-formedness on hand-authored data files)
- [Official docs over local examples](feedback_official_docs_over_local_examples.md) — for spec-governed files (skill frontmatter, config formats), fetch the authoritative docs, don't imitate existing local files
- [Prefer jq for JSON](feedback_prefer_jq_for_json.md) — use jq for shell JSON queries when available; save Python for genuinely complex parsing (e.g. XML)
- [Plan requirements: tests + docs always required](feedback_plan_requirements.md) — every implementation plan must include tests and docs sections; never omit
- [Podman repo org moved](reference_podman_repo_org.md) — podman/buildah/skopeo/container-libs are now under podman-container-tools/, not containers/
- [Repo-root terminology](feedback_repo_root_terminology.md) — "repo-root" = main working tree, NOT a linked worktree under `worktrees/`
- [CHANGELOG header rule](feedback_changelog_header_rule.md) — a version appears in a `##` header iff tag exists OR body names it; bracket iff tagged; no gap-fillers
- [Review numbering](feedback_review_numbering.md) — in `/review` and `/security-review`, use continuous numbering across the whole review; sub-points use letters, not bullets
- [Commit messages](feedback_commit_messages.md) — prefer brevity (limits are ceilings), always add Co-Authored-By when Claude assisted (model + version + email, no context-window notes), design against repo commitlint config
- [File renames need `git mv`](feedback_file_renames.md) — plain `mv` on tracked files is a git-shaping action; hand `git mv` to the user
- [Default to least privilege](feedback_least_privilege.md) — omit broad-privilege settings unless a specific need forces them; start minimal, escalate as a targeted fix
- [Ansible test DRY](feedback_ansible_test_dry.md) — role-internal constants belong in vars/main.yml; tests load via task helper, never duplicate into fixtures
- [Review output shape](feedback_review_output_shape.md) — a review is read-only analysis: never a plan file, never `ExitPlanMode`; What I verified → Findings → Verdict, then ask before showing "Not raised" / write actions needing consent
