# Description

This repo is just a collection of template and project specific Vagrantfiles that I use, and
which I thought others might get use from. I'm not looking to start a huge project. I made
some design choices that others might find questionable (for example, I deliberately skipped
taking advantage of the [File Provisioner](https://developer.hashicorp.com/vagrant/docs/provisioning/file) in favor of making completely self
contained Vagrantfiles). So, while I'm definitely open to feedback and PRs (particularly for
security issues), you will probably find it best to just fork/copy/mod what suits your fancy.

Happy Virtualizing,
H Dub

---
# Requirements
* [Vagrant](https://developer.hashicorp.com/vagrant/docs/installation)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
  * [VirtualBox Guest Additions](https://www.virtualbox.org/manual/ch04.html#additions-linux) - for shared directories between host and guest.

---
# Available files

## Templates

All Templates are customizeable with a few variables at the top of their respective Vagrantfiles.

| Template | Description |
| --- | --- |
| [python](python) | Using [debian/bookworm64](https://app.vagrantup.com/debian/boxes/bookworm64), update the system, install specified apt_packages as well as optional Python module dependencies, install and setup pyenv for the vagrant user, install the specified version of Python and set it for the local project, create a venv for the project with the specified python_version if it doesn't exist and execute the project setup script specified by the user |

## Project Specific

| Project | Vagrantfile | Template |
| --- | --- | --- |
