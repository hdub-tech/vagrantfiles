# Kali Vagrantfile

* [Description](#description)
* [Variables](#variables)
* [Changelog](#changelog)

---
## Description

This [Vagrantfile](Vagrantfile) is based on the Official [kalilinux/rolling](https://app.vagrantup.com/kalilinux/boxes/rolling) box. It performs the actions listed below, in that order, as the user specified (The "root or vagrant" steps depend on the `$run_as_root` variable). Refer to [Variables](#variables) for more info on the `$variables`.

  0. Load the [`defaults.yml`](defaults.yml), and `overrides.yml`, if it exists.
  1. Create VM with the name `$vm_name` in VirtualBox.
  2. Pull down the [kalilinux/rolling](https://app.vagrantup.com/kalilinux/boxes/rolling) Vagrant box.
  3. Set hostname to `$hostname`.
  4. Sync `$local_shared_dir` on the host to `/vagrant` in the guest Kali VM.
  5. If `$bridged_adapter` is NOT set to `disabled`, creates a bridged_adapter to the interface specified by `$bridged_adapter`.
  6. Disable the ["VirtualBox Remote Desktop" server](https://www.virtualbox.org/manual/ch07.html#vrde) which Kali(???) is enabling???
  7. (root) Update Kali keyring, set `grub-pc` installed_devices to the disk `/boot/grub` is on, check for updated packages, upgrade system packages, and install the specified `$apt_packages` as well as the Python apt dependencies for required and optional modules.
  8. (root) Change the login shell to `$preferred_shell` for (root or vagrant) depending on `$run_as_root`.
  9. (root or vagrant) If `~/.pyenv` does not exist, install [pyenv](https://github.com/pyenv/pyenv) via the hated pipe to bash method. Add the pyenv setup config to `/etc/profile.d/pyenvrc`, and source that file from `~/.profile` and the `$preferred_shell` home rc file.
  10. (root or vagrant) Install `$python_version` using [`pyenv`](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-install) and set it as the `pyenv global` version. Install the specified `$pip_packages` into that `pyenv`, and upgrade its pip and pipx
  11. (root or vagrant) If a list of `$git_repos` is specified, clone them to `$git_repos_dir`, if they are not already there. See Variable footnote [#3](#3) for some implementation details.
  12. (root or vagrant) If a list of `$pipx_projects` is specified, run `pipx install` to install them into the `pyenv global` `$python_version` environment and then run `pipx ensurepath` to make sure they are accessible upon login.
  13. (root or vagrant) If a list of `$curl_bash_projects` is specified, run `curl $url | bash` to install them.
  14. (root or vagrant) If a list of `$compressed_app_urls` is specified, run `wget` to download and the appropriate extraction tool to decompress them to `$apps_dir` OR `dpkg --install` them if they have a `.deb` extension, if they are not already there.
  15. (root or vagrant) Copy in the [shortcuts](shortcuts) to the (root or vagrant) user's Desktop and mark them "secure". NOTE: Shortcut files support `$apps_dir` and `$git_repos_dir` variables (_Issue [#8](https://github.com/hdub-tech/vagrantfiles/issues/8) to make this optional_).
  16. (root or vagrant) Run the user specified `$custom_setup_script` and copy that script to `/vagrant/vagrant-custom-setup.sh`. See the [Variables](#variables) chart for the default actions of this script.

## Variables

This chart contains the list of customizeable variables used by the Vagrantfile. To keep it [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), rather than document default values here, please refer to the [defaults.yml](defaults.yml) file.

> [!WARNING]
> DO NOT MODIFY `defaults.yml` DIRECTLY. To change values, copy it to a file named `overrides.yml` and update that file.

| Variable | Type | Description |
| --- | --- | --- |
| `vm_name` | String | Used as VM name |
| `hostname` | String | Used as VM hostname |
| `bridged_adapter` | String | Which interface on the host to bridge to the VM. Needed if doing network scanning. Set to `disabled` to skip. |
| `local_shared_dir` | String | The directory on the system which will be mounted to `/vagrant` in the VM |
| `run_as_root` | boolean | Whether or not to run the shell provisioners as root<sup>[1](#1)</sup>. If false, uses the `vagrant` user |
| `preferred_shell` | String | Name, not path<sup>[2](#2)</sup>. Used to change the login shell for the (root or vagrant) user (based on `$run_as_root`), in order to update `$HOME/.*rc` files appropriately for pyenv and pipx |
| `apt_packages` | Array[String] | The packages you want installed in the VM. Python apt dependencies are already included, and do not need to be listed here. |
| `git_repos_dir` | String | The full path to the directory you want to clone github repos into<sup>[3](#3)</sup> |
| `git_repos` | Array[String] | The https URLs to the git repos the user wants to clone. Do NOT include the `.git` at the end as the basename of the URL will be used to determine if it is already cloned. |
| `python_version` | String | Any [pyenv supported version](https://github.com/pyenv/pyenv/tree/master/plugins/python-build/share/python-build) of Python, including the unlisted `major` or `major.minor` versions, like `3` or `3.12`. This will be set as the `pyenv global` default. |
| `pip_packages` | Array[String] | The `pip` packages to install into the `pyenv global` default (`pipx` will be installed here already). |
| `pipx_packages` | Array[String] | A list of directories (full path) which contain Python projects on which `pipx install` will be executed to install them into the `pyenv global` default. `$git_repos_dir` and `$apps_dir` variables supported. |
| `apps_dir` | String | The directory to download and decompress archives into. |
| `compressed_app_urls` | Array[JSON] | The URLs of `zip`/`.tar.gz`/`.deb` files to download to `$apps_dir` (with OPTIONAL checksum verification) and extract to the same directory OR `dpkg --install` if `.deb`. JSON format is as follows with only `url` key required and algorithm is any supported by the [`cksum`] command: `{ url: 'https://example.com/app.zip', algorithm: sha256, checksum: 92789c0bd3e1f75b1a03d80e74eb7ea8d3a95e19f7915557774a0ad402674926}` |
| `curl_bash_projects` | Array[String] | The URLs of sites to run `curl $url \| bash` with |
| `custom_setup_script` | Heredoc (Squiggly Unquoted)<sup>[4](#4)</sup> | This is where your custom setup script goes. By default, this will setup sdkman, install a cfr compatible version of java and maven, compile and test cfr, and create a non-versioned symlink for Intellij Idea |

---
Footnotes:

* <a id=1>1</a> - The apt install and chsh provisioners will be run as root regardless of this setting.
* <a id=2>2</a> - Example: `bash`. Only tested with `bash` and `zsh`.
* <a id=3>3</a> - If `$run_as_root` is false and the non-root user doesn't have write perms in the specified `$git_repos_dir`, `sudo` will be used with the `git clone` operation seeing as the Vagrantfile can run sudo anyways. As an example, `/opt` is a traditional directory to throw optional additional software, and the default for this Vagrantfile, but the `vagrant` user cannot write there without `sudo`. The cloned directories (NOT `$git_repos_dir` itself), will have their ownership changed to the non-root user in this situation.
* <a id=4>4</a> Aka Interpolation and Esacaping is enabled, indentation is allowed but will be cleaned up on the final script.

---
## Changelog

* 1.0 - Initial Release
* 2.0 - Add support for default.yml/overrides.yml, configurable bridged adapter, curl_bash_projects, zip/tarballs download/extraction, using apps_dir/git_repos_dir in pipx_packages and custom_setup_script, Desktop shortcuts. Renamed default VM/hostname, upgraded Python, fixed missing primary_user ref, fixed Kali apt key, fixed grub-pc upgrade error, added/installed more tools
* 3.0 - Add support for `.deb` files, improve `custom_setup_script` output.
* 4.0 - Add support for BinaryNinja desktop shortcut with icon, checksum verification for `compressed_app_urls`, udpated pyenv bash env setup per new guidance, switch from no longer available procyon-decompiler to bytecode-viewer (which has procyon builtin), switch from deprecated IDEA Community to combined IDEA, update version of nomachine.

<!-- Links -->
[`cksum`]: https://man7.org/linux/man-pages/man1/cksum.1.html
