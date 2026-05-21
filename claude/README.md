# Claude Vagrantfile

* [Description](#description)
* [Variables](#variables)
* [Per-user opinionated defaults](#per-user-opinionated-defaults)
* [Changelog](#changelog)

---
## Description

This [Vagrantfile](Vagrantfile) is based on the
[bento/ubuntu-24.04](https://app.vagrantup.com/bento/boxes/ubuntu-24.04) box.
It performs the actions listed below, in that order, as the user specified
in parenthesis (where relevant). Refer to [Variables](#variables) for more
info on the `$variables`.

  1. Pull down the
     [bento/ubuntu-24.04](https://app.vagrantup.com/bento/boxes/ubuntu-24.04)
     Vagrant box.
  2. Set hostname to `$project_name` (dots replaced with dashes).
  3. Sync `$local_shared_dir` to `$vm_mountpoint`.
  4. Create VM named `$project_name-bento-ubuntu-2404` in VirtualBox with
     16384 MB RAM, 4 CPUs, a 20 GB primary disk, 128 MB video memory, and
     VRDE disabled.
  5. (root) Update apt with `DEBIAN_FRONTEND=noninteractive` and dpkg
     `--force-confdef` / `--force-confold` flags, upgrade system packages,
     and install the specified `$apt_packages` (the concatenation of
     `$podman_packages`, `$python_packages`, and `$must_have_packages`).
  6. (vagrant) For each URL in `$git_repos`, `git clone` it into
     `$git_repos_dir`. Existing target directories are skipped.
  7. (vagrant) If `~/.pyenv` does not exist, install pyenv via the hated
     pipe-to-bash method. Write the pyenv init config to `~/.pyenvrc` and
     source that file from `~/.bashrc`.
  8. (vagrant) Install `$python_version` using
     [`pyenv`](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-install)
     and set it as the global Python version.
  9. (vagrant) Run `$podmans_setup_script`: check out `$podman_version`
     (or the latest non-pre-release tag), install the Go version named in
     `podman/go.mod`, build conmon, write `/etc/containers/registries.conf`
     and `policy.json`, regenerate the `en_US.UTF-8` locale, build podman
     from source, patch AppArmor to work around
     [containers/podman#24642](https://github.com/containers/podman/issues/24642),
     check out `$podman_compose_version` (or latest), and build
     podman-compose. The rendered script is mirrored to
     `$vm_mountpoint/vagrant-podmans-setup.sh` so it can be re-run by hand.
  10. (vagrant) Run `$project_setup_script`: download Claude Code CLI
     `$claude_version` over HTTPS, verify its SHA256 against the official
     release manifest, run `claude install`, and append the
     `CUSTOM_CLAUDE_FLAGS` exports and `claude` alias to `~/.customrc`.
     Seed the [opinionated defaults](#per-user-opinionated-defaults) if the
     corresponding target files do not already exist. The rendered script
     is mirrored to `$vm_mountpoint/vagrant-project-setup.sh`.

---
## Variables

This chart is the list of variables in the top of the Vagrantfile which you
are encouraged to update based on your needs. To keep it
[DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), rather than
document default values here, please just see the corresponding
[Vagrantfile](Vagrantfile#L22-L202). Updating Variables listed as "computed" is
not recommended.

| Variable | Type | Description |
| --- | --- | --- |
| `claude_version` | String | Pinned Claude Code CLI version to install. The SHA256 is fetched at install time from the official release manifest. Should be ≥ 1 week old to avoid not-yet-detected supply-chain compromises. To update: run the (COMING SOON) `upgrade-dependencies` skill. |
| `project_name` | String | Used as VM hostname and part of the VM name. Defaults to `claude-$claude_version`. |
| `claude_md_source` | String (computed) | Selects which user-level `CLAUDE.md` template is seeded into `~/.claude/CLAUDE.md`. Evaluates to `CLAUDE.md.overrides` if that file exists alongside the Vagrantfile, otherwise `CLAUDE.md.defaults`. To customize without modifying the tracked default, copy `CLAUDE.md.defaults` to `CLAUDE.md.overrides` (gitignored) and edit only that file. |
| `local_shared_dir` | String | Folder on your host system to share at `$vm_mountpoint` inside the VM. Defaults to `"../"` (the repository root). For git worktree layouts, uncomment the alternative `"../../../"`. |
| `vm_mountpoint` | String | In-guest path that `$local_shared_dir` is mounted to. Defaults to `/vagrant`. |
| `podman_packages` | String (space separated)<sup>[1](#1)</sup> | apt packages needed to build podman from source. |
| `python_packages` | String (space separated)<sup>[1](#1)</sup> | apt packages needed by pyenv to build Python from source. |
| `must_have_packages` | String (space separated)<sup>[1](#1)</sup> | apt packages the author considers essential on every VM. |
| `apt_packages` | String (computed) | Concatenation of `$podman_packages`, `$python_packages`, and `$must_have_packages`. |
| `python_version` | String | Any [pyenv supported version](https://github.com/pyenv/pyenv/tree/master/plugins/python-build/share/python-build) of Python, including the unlisted `major` or `major.minor` versions, like `3` or `3.12`. |
| `python_version_abbrev` | String | A tox-style target abbreviation derived from `$python_version` (e.g., `py314` for `3.14`). If you use something like `miniconda`, the auto-derived name might be mangled; in that case, manually hardcode it. |
| `git_repos_dir` | String | Directory inside the VM that `$git_repos` are cloned into. Defaults to `/home/vagrant`. |
| `git_repos` | Array of String | List of `https` git URLs to clone into `$git_repos_dir`. Existing target directories are skipped (not pulled). |
| `podman_version` | String (optional) | If set, check out this exact tag of podman before building (e.g., `4.9.3`). If unset (the default), the latest non-pre-release tag is used. |
| `podman_compose_version` | String (optional) | Same as `$podman_version` but for podman-compose. |
| `podmans_setup_script` | Heredoc (Squiggly Unquoted)<sup>[2](#2)</sup> | Embedded podman / podman-compose build script. Mirrored to `$vm_mountpoint/vagrant-podmans-setup.sh` in case  it needs to be re-run. Modification of this is not recommended. |
| `project_setup_script` | Heredoc (Squiggly Unquoted)<sup>[2](#2)</sup> | Embedded project setup script: Claude Code install, `.customrc` setup, and opinionated defaults seeding. Mirrored to `$vm_mountpoint/vagrant-project-setup.sh` in case it needs to be re-run. |

---
Footnotes:

* <a id=1>1</a> - I deliberately went with a string as opposed to an
  array to make this easier for users to populate. This also includes a
  few things that **_I_** like on everything and which you might not
  care for.
* <a id=2>2</a> - Unquoted squiggly heredoc — Ruby interpolation and
  escaping are **enabled**, and indentation is allowed but will be
  cleaned up on the final script.

---
## Per-user opinionated defaults

Two files are seeded into the VM by `$project_setup_script` on first
provision only. Each copy is gated by an existence check, so re-running
`vagrant up --provision` will not clobber files you have already edited.
To re-seed from the tracked source, delete the target inside the VM and
re-provision.

| Source (host) | Target (guest) | How to customize |
| --- | --- | --- |
| `claude/CLAUDE.md.defaults` (or `claude/CLAUDE.md.overrides` if present) | `~/.claude/CLAUDE.md` | Copy `CLAUDE.md.defaults` to `CLAUDE.md.overrides` on the host and edit only that file. `CLAUDE.md.overrides` is gitignored. |
| `$vm_mountpoint/.claude/settings.example.json` | `$vm_mountpoint/.claude/settings.local.json` | Edit `settings.local.json` directly. It lives on the synced folder, so host and guest see the same file. |

---
## Changelog

* 1.0 - Initial Release
