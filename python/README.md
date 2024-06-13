# Python Vagrantfile

* [Description](#description)
* [Variables](#variables)
* [Changelog](#changelog)

---
## Description

This [Vagrantfile](Vagrantfile) is based on the Official [debian/bookworm64](https://app.vagrantup.com/debian/boxes/bookworm64) box. It performs the actions listed below, in that order, as the user specified in parenthesis (where relevant). Refer to [Variables](#variables) for more info on the `$variables`.

  1. Pull down the [debian/bookworm64](https://app.vagrantup.com/debian/boxes/bookworm64) Vagrant box.
  2. Set hostname to `$project_name`.
  3. Create VM with the name `$project_name-debian12-bookworm` in VirtualBox.
  4. (root) Check for updated packages, upgrade system packages, and install the specified `$apt_packages` as well as the Python apt dependencies for required and optional modules.
  5. (vagrant) If `~/.pyenv` does not exist, install pyenv via the hated pipe to bash method. Add the pyenv setup config to `~/.pyenvrc`, and source that file from `~/.bashrc`.
  6. (vagrant) Install `$python_version` using [`pyenv`](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-install) and set it as the local version for the project in `/vagrant`.
  7. (vagrant) Create a [venv](https://docs.python.org/3/library/venv.html#creating-virtual-environments) in `$venv_dir` with `$venv_prompt` and set it to activate on login.
  8. (vagrant) Run the user specified `$project_setup_script` and copy that script to `/vagrant/vagrant-project-setup.sh`.

---
## Variables

This chart is the list of variables in the top of the Vagrantfile which you are encouraged to update based on your needs. To keep it [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), rather than document default values here, please just see the corresponding [Vagrantfile](Vagrantfile#L6-L41).

| Variable | Type | Description |
| --- | --- | --- |
| `project_name` | String | Used as VM hostname and part of the VM name |
| `python_version` | String | Any [pyenv supported version](https://github.com/pyenv/pyenv/tree/master/plugins/python-build/share/python-build) of Python, including the unlisted `major` or `major.minor` versions, like `3` or `3.12` |
| `python_version_abbrev` | String | Used as part of the `venv_dir` name, intended to replicate tox targets, like `py312` for Python 3.12. If you use something like `miniconda`, or any of the other named Pythons which pyenv supports, this name might be mangled into something like `pyminiconda3-312-24`. In this case, you might want to manually hardcode it to something like `py312` instead. |
| `create_new_project_venv` | boolean | Whether or not to create a new `venv_dir` with prompt `venv_prompt` and activate it on login. If `false`, no venv actions will occur. If `true` and the `venv_dir` already exists, venv creation is skipped but it will be activated on login. |
| `venv_dir` | String | If `create_new_project_venv` is true, this will be the path to the directory |
| `venv_prompt` | String | If `create_new_project_venv` is true, this will be the prompt in the venv. |
| `apt_packages` | String (space separated)<sup>[1](#1)</sup> | The packaged which need to be installed in order to setup your project. You do NOT need to handle the Python dependencies here, just yours. |
| `project_setup_script` | Heredoc (Squiggly Quoted)<sup>[2](#2)</sup> | This is where your project specific setup script goes. Examples include things like `pip install -e .` and `pip install tox` |

---
Footnotes:

  * <a id=1>1</a> - I deliberately went with a string as opposed to an array to make this easier for users to populate. This also includes a few things that **_I_** like on everything and which you might not care for.
  * <a id=2>2</a> - Aka Interpolation and Esacaping is disabled, indentation is allowed but will be cleaned up on the final script. I didn't see a need for using Ruby variables in this and I usually want my scripts to be just like I wrote them, but I also like to indent for cleaner Vagrantfiles.

---
## Changelog

* 1.0 - Initial Release
