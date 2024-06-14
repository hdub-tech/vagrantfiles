# Kali Vagrantfile

* [Description](#description)
* [Variables](#variables)
* [Changelog](#changelog)

---
## Description

This [Vagrantfile](Vagrantfile) is based on the Official [kalilinux/rolling](https://app.vagrantup.com/kalilinux/boxes/rolling) box. It performs the actions listed below, in that order, as the user specified (The "root or vagrant" steps depend on the `$run_as_root` variable). Refer to [Variables](#variables) for more info on the `$variables`.

  1. Create VM with the name `$vm_name` in VirtualBox.
  2. Pull down the [kalilinux/rolling](https://app.vagrantup.com/kalilinux/boxes/rolling) Vagrant box.
  3. Set hostname to `$hostname`.
  4. Sync `$local_shared_dir` on the host to `/vagrant` in the guest Kali VM.
  5. Disable the ["VirtualBox Remote Desktop" server](https://www.virtualbox.org/manual/ch07.html#vrde) which Kali(???) is enabling???
  6. (root) Check for updated packages, upgrade system packages, and install the specified `$apt_packages` as well as the Python apt dependencies for required and optional modules.
  7. (root or vagrant) If `~/.pyenv` does not exist, install [pyenv](https://github.com/pyenv/pyenv) via the hated pipe to bash method. Add the pyenv setup config to `/etc/profile.d/pyenvrc`, and source that file from `~/.profile` and the `$preferred_shell` home rc file.
  8. (root or vagrant) Install `$python_version` using [`pyenv`](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-install) and set it as the `pyenv global` version. Install the specified `$pip_packages` into that `pyenv`, and upgrade its pip.
  9. (root or vagrant) If a list of `$git_repos` is specified, clone them to `$git_repos_dir`. See Variable footnote [#3](#3) for some implementation details.
  10. (root or vagrant) If a list of `$pipx_projects` is specified, run `pipx install` to install them into the `pyenv global` `$python_version` environment and then run `pipx ensurepath` to make sure they are accessible upon login.
  11. (root or vagrant) Run the user specified `$custom_setup_script` and copy that script to `/vagrant/vagrant-custom-setup.sh`.

---
## Variables

This chart is the list of variables in the top of the Vagrantfile which you are encouraged to update based on your needs. To keep it [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), rather than document default values here, please just see the corresponding [Vagrantfile](Vagrantfile#L6-L70).

| Variable | Type | Description |
| --- | --- | --- |
| `vm_name` | String | Used as VM name |
| `hostname` | String | Used as VM hostname |
| `preferred_shell` | String | Name, not path<sup>[1](#1)</sup>. Used to update `$HOME/.*rc` files appropriately for pyenv and pipx, and optionally with `chsh` |
| `local_shared_dir` | String | The directory on the system which will be mounted to `/vagrant` in the VM |
| `run_as_root` | boolean | Whether or not to run the shell provisioners as root<sup>[2](#2)</sup>. If false, uses the vagrant user |
| `python_version` | String | Any [pyenv supported version](https://github.com/pyenv/pyenv/tree/master/plugins/python-build/share/python-build) of Python, including the unlisted `major` or `major.minor` versions, like `3` or `3.12`. This will be set as the `pyenv global` default |
| `apt_packages` | Array[String] | The packages you need/want installed in the VM. You do NOT need to handle the Python dependencies here, just yours. |
| `git_repos_dir` | String | The full path to the directory you want to clone github repos in to<sup>[3](#3)</sup> |
| `pip_packages` | Array[String] | The `pip` packages to install into the `pyenv global` default |
| `pipx_packages` | Array[String] | A list of directories (full path) which contain Python projects which `pipx install` will be executed to install them into the `pyenv global` default |
| `custom_setup_script` | Heredoc (Squiggly Unquoted)<sup>[4](#4)</sup> | This is where your custom setup script goes. Examples include things like `chsh -s ` and adding a `cd /vagrant` to the `$preferred_shell` rc file |

---
Footnotes:

  * <a id=1>1</a> - Example: `bash`. Only tested with `bash` and `zsh`.
  * <a id=2>2</a> - The apt install provisioner will be run as root regardless of this setting.
  * <a id=3>3</a> - If `$run_as_root` is false and the non-root user doesn't have write perms in the specified `$git_repos_dir`, `sudo` will be used with the `git clone` operation seeing as the Vagrantfile cam run sudo anyways. As an example, `/opt` is a traditional directory to throw optional additional software, and the default for this Vagrantfile, but the `vagrant` user cannot write there without `sudo`. The cloned directories (NOT `$git_repos_dir`), will have their ownership changed to the non-root user.
  * <a id=4>4</a> Aka Interpolation and Esacaping is enabled, indentation is allowed but will be cleaned up on the final script.

---
## Changelog

* 1.0 - Initial Release
