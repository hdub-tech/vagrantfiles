* [Description](#description)
* [Requirements](#requirements)
* [General Usage](#general-usage)
* [Available files](#available-files)
  * [Templates](#templates)
  * [Project Specific](#project-specific)

# Description

This repo is just a collection of template and project specific Vagrantfiles that I created
as a way to learn Vagrant and more quickly stand-up development VMs for working on other
projects. I'm not looking to start a huge project, but I thought others might get use from them.
I made some design choices that others might find questionable (for example, I deliberately skipped
taking advantage of the [File Provisioner](https://developer.hashicorp.com/vagrant/docs/provisioning/file) in favor of making completely self
contained Vagrantfiles). For the [project-specific](project-specific) ones, I don't do a fresh clone
of the project but rather attach a pre-existing clone to /vagrant (That said the script used to
set-up the project is copied to the VM so you can always clone fresh and re-run the script).
So, while I'm definitely open to feedback and PRs (particularly for security issues), you will
probably find it best to just fork/copy/mod what suits your fancy.

Happy Virtualizing,
H Dub

---
# Requirements
* [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  * [VirtualBox Guest Additions](https://www.virtualbox.org/manual/ch04.html#additions-linux) - for shared directories between host and guest.

---
# General Usage

Below is the general usage steps for these files. More specific instructions are included with each Vagrantfile.

  1. Copy or symlink the desired Vagrantfile to the top level of the project you want to use it with.

  2. **_IF_** it is **_NOT_** project-specific, update the variables in the block at the top of the Vagrantfile.

  3. Run `vagrant up` in your project directory.

---
# Available files

## Templates

All Templates are customizeable with a few variables at the top of their respective Vagrantfiles. Click on the
Template name for a more detailed README.

| Template | Version | Description |
| --- | --- | --- |
| [python](python) | 1.0 | Using [debian/bookworm64](https://app.vagrantup.com/debian/boxes/bookworm64), update the system, install specified apt_packages as well as optional Python module dependencies, install and setup pyenv for the vagrant user, install the specified version of Python and set it for the local project, create a venv for the project with the specified python_version if it doesn't exist and execute the project setup script specified by the user. |

## Project Specific

| Project | Vagrantfile | Version | Template |
| --- | --- | --- | --- |
