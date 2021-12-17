# Installation

Installation of the Data Library software is automated using [ansible](https://github.com/ansible/ansible), a configuration management tool.
Automating the installation and configuration process has the following advantages over doing it by hand:
* Convenience: an automated installation process takes less of the system administrator's time.
* Repeatability: having relevant system configuration details documented in executable form makes it easier to reproduce the same configuration on another server, *e.g.* after a hardware failure or upgrade.

The above advantages could be achieved by automating the installation process with a bash script, but using ansible instead of bash brings further advantages. The same ansible "playbook" (configuration management script) that performs the initial software installation can also be used subsequently to manage the server's configuration.
* When the server configuration needs to change, those changes can be described in the playbook and checked into version control, so there is a record of what was changed and when. Running the playbook then applies the changes to the server.
* An ansible playbook can be run in "check mode". In this mode, the playbook makes no changes, but merely reports any differences between the server's configuration and the desired state. Knowing what changes the tool will make before it makes them helps avoid some kinds of configuration errors.

## Recommended hardware
The following hardware configurations have been found to give good performance:

Basic Requirements:
* 1x Intel Xeon E-series 8 core
* Dual 1GB Ethernet
* 32-64 GB RAM
* 128GB OS Hard Drive
* (2) internal 4 TB HD for data. Mirror RAID
* (1) External 8TB HD for backups

High End Requirements:
* 2x Intel Xeon Silver 4208 or better
* Dual 10GB Ethernet
* 64-128 GB RAM
* 128GB OS HD
* (2) internal 4 TB HD for data. Mirror RAID
* (1) External 8TB HD for backups

We recommend a server that has at least 4 internal 3.5” HD slots for future expansion. A 1250VA uninterruptible power supply (UPS) is also recommended.

## Prepare the server
Before running the installation script, perform the following steps to prepare the server:
* Install CentOS 7 (minimal server configuration). Note that Red Hat has terminated support for CentOS 8 prematurely, while maintenance updates will still be available for CentOS 7 through June 2024. Consequently, plans to migrate the Data Library to CentOS 8 have been abandoned. If you have thoughts about what we should target as the next platform after CentOS 7, please communicate them to [help@iri.columbia.edu](mailto:help@iri.columbia.edu).
* Create a user account for the system administrator who will be performing the installation. Make that user a member of the `wheel` group so that they will be able to perform commands as root using `sudo`.
* Mount a volume with at least 1TB of storage space, preferably with mirror RAID, at `/data`. List the volume in `/etc/fstab` to ensure that it will be mounted at boot time. We recommend using LVM to create logical volumes, and formatting the volume with XFS. Note that an XFS filesystem can be expanded but not shrunk, so it may be preferable to leave some disk space unallocated, to be used for snapshots or unanticipated storage needs, rather than putting all of the available space into the XFS-formatted volume.

## Create a configuration repository
The next step is to retrieve a copy of the installation script and customize it for your site.
* TODO Create a repo from template.
* Create an ssh key pair and configure it to have read-only access to your maproom and dlentries git repositories (see Data Library User Guide). See instructions for [bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/add-access-keys/), [github](https://docs.github.com/en/developers/overview/managing-deploy-keys#deploy-keys). Store the key pair somewhere outside of your git repository (secrets should not be stored in plaintext in git), and set the variable `local_git_key_file` in `playbook.yaml` to the absolute path of the private key file.
* Customize `inventory.yaml` and `playbook.yaml`, following the comments in those files.


## Install ansible
We typically install and run ansible on a developer's laptop, and configure the server from the laptop over ssh. Ansible can also be installed and run on the server itself, but we will not cover that configuration here.

* Create a python 3 virtual environment in which to install ansible. For example, `mkdir ~/venv && python3 -m venv ~/venv/ansible`
* Activate the virtual environment: `source ~/venv/ansible/bin/activate`
* In the virtual environment, Upgrade `pip` to the latest available version and install `wheel`: `pip install -U pip wheel`
* Install ansible: `pip install ansible==4.5.0` (newer versions might work but are untested)

## Run the ansible playbook
From the root directory of the configuration repository:
```
ansible-playbook \
    --ask-become-pass \
    -i inventory.yaml \
    -e @~/dlsecrets.yaml \
    -e @run_update_script=yes \
    datalibrary.yaml
```
