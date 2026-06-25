# Description

This repo is just a collection of template and project specific Vagrantfiles that I created as a way to learn Vagrant and more quickly stand-up development VMs for working on other projects. I'm not looking to start a huge project, but I thought others might get use from them.

I made some design choices that others might find questionable. For example, I deliberately skipped taking advantage of the [File Provisioner](https://developer.hashicorp.com/vagrant/docs/provisioning/file) in some places in favor of making completely self contained Vagrantfiles. For the [project-specific](project-specific) ones, I don't do a fresh clone of the project but rather attach a pre-existing clone to /vagrant (That said, the script used to set-up the project is copied to the VM so you can always clone fresh and re-run the script). And possibly most obviously, I'm doing a ton in shell provisioners which would be better suited for a configuration management tool like Ansible, which I know pretty intimately even. I'm just seeing how far I can push the monolithic Vagrantfile without additional tools required.

So, while I'm definitely open to feedback and PRs (particularly for security issues), you will probably find it best to just fork/copy/mod what suits your fancy.

Happy Virtualizing,
H Dub

* [Description](#description)
* [Requirements](#requirements)
* [General Usage](#general-usage)
* [Available files](#available-files)
  * [Templates](#templates)
  * [Project Specific](#project-specific)

---
# Requirements

Install:

* [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  * [VirtualBox Guest Additions](https://www.virtualbox.org/manual/ch04.html#additions-linux) - for shared directories between host and guest.

---
# General Usage

Below is the general usage steps for these files. More specific instructions are included with each Vagrantfile.

  1. For [`project-specific`](project-specific) VMs, copy or symlink the desired Vagrantfile to the top level of the project you want to use it with (for example, copy or symlink it where you cloned the [`podman`](https://github.com/containers/podman) project for the [podman](project-specific/containers/podman) VM).

  2. Update customizeable settings, if desired:

     a. For templates, like the [Kali VM](kali), copy the `defaults.yml` file to `overrides.yml`, and update the desired values.

     b. For [`project-specific`](project-specific) VMs, you shouldn't need to do anything, but if you really need to, update the desired variables in the block at the top of the Vagrantfile.

  3. Run `vagrant up` in the directory containing the Vagrantfile.

---
# Available files

## Templates

All Templates are customizeable with an overrides.yml file, or a few variables
at the top of their respective Vagrantfiles. Click on the Template name for a
more detailed README.

| Template | Version | Why Use | Description |
| --- | --- | --- | --- |
| [python](python) | 1.0 | Copy and use in a Python based project | Using [debian/bookworm64](https://app.vagrantup.com/debian/boxes/bookworm64): <ul><li>update the system</li><li>install specified apt_packages as well as optional Python module dependencies</li><li>install and setup pyenv for the vagrant user</li><li>install the specified version of Python and set it for the local project</li><li>create a venv for the project with the specified python_version if it doesn't exist and execute the project setup script specified by the user</li></ul> |
| [kali](kali) | 4.0 | General hackery | Using [kalilinux/rolling](https://app.vagrantup.com/kalilinux/boxes/rolling): <ul><li>set specified VM name, hostname, shared folder, and bridged adapter</li><li>Pull kali keyring, fix grub-pc install disk, run apt update and upgrade, install specified apt packages</li><li>Set preferred shell</li><li>install and setup pyenv, the specified version of Python (and set as global), and the specified pip packages</li><li>checkout the specified git repos to the specified dir</li><li>pipx install the specified projects</li><li>`curl\|bash` install specified URLs</li><li>download and extract specified tarballs and zips, as well as install debs, with optional checksum verification on the downloads</li><li>copy in some desktop shortcuts</li><li>execute a custom script on provision, which by default installs Java and maven with sdkman in order to compile cfr</li></ul> |

## Project Specific

Project specific VMs are meant to correspond to Github projects which don't already have a Vagrantfile.

| Project | Vagrantfile | Version | Template |
| --- | --- | --- | --- |

## Contributing

I can't imagine anyone would be interested in this, but I'm in the process of
adding some linting pre-commit hooks, and would ask that **new** changes pass.
To have them automatically run, execute:

```
# from anywhere in the project
git config core.hooksPath "$( git rev-parse --git-dir )"/.githooks
```

> [!IMPORTANT]
> The commitlint hook requires either `podman` or `npx` (Node) to be available.
> Dealer's choice. If you have `docker`, you should be able to just
> `alias podman=docker` and it *should* work. (lmk ha).
