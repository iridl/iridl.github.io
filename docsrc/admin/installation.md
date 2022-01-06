# Installation
This section provides instructions for installing the Data Library software on a server.

Installation of the Data Library software is automated using [ansible](https://docs.ansible.com/ansible_community.html), a configuration management tool.
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
* Install ansible and git:

        sudo yum install centos-release-ansible29  # enables additional yum repository
        sudo yum install -y ansible git

* Disable SELinux and reboot:

        sudo sed -i s/SELINUX=enforcing/SELINUX=disabled/ /etc/selinux/config
        sudo shutdown -r now

```{note}
If you feel SELinux is important to the security of your server, please start a conversation with us at help@iri.columbia.edu.
```

## Create a configuration repository
* Create a git repository to track your Data Library configuration. At IRI we call ours `dlconfig`.

        mkdir dlconfig
        cd dlconfig
        git init

* Inside the new git repository, install the IRIDL ansible collection:

        ansible-galaxy collection install \
            -p . \
            git+https://github.com/iridl/iridl-ansible.git

* The previous command should have downloaded the collection to a subdirectory called `ansible_collections`. Add and commit that directory to your git repository, to ensure that you will use the same version of the collection every time you run the playbook.

        git add ansible_collections
        git commit -m "add iridl ansible collection"

* Copy template configuration files from the collection to the top level of the repository:

        cp ansible_collections/iridl/iridl/example/* .

* Move `secrets.yaml` out of the git repository. For security reasons, unencrypted secrets should not be committed to version control.

        mv secrets.yaml ..

* Customize `inventory.yaml`, `playbook.yaml`, and `../secrets.yaml` following the comments in those files. There are references in the comments to "dlentries" and "maproom" repositories. You will need to work with IRI staff to create these repositories. Commit your customizations and push them to your git server for safe keeping; back up `secrets.yaml` by other means, such as copying it to another machine.

Never edit the contents of the `ansible_collections` directory. All customization should be made in the configuration files that you copied from the template. In the future when it comes time to upgrade to a newer version of the DL software, you will run the `ansible-galaxy` command again and commit the new version to your configuration repository. Don't upgrade without checking the release notes first, because in some cases an upgrade may require manual migration steps. (At this writing, there are no upgrade release notes because this is the playbook's initial release.)

## Run the ansible playbook
Now we are ready to run the playbook, which will download, configure, and install the Data Library software using the parameters you defined in the configuration files.

From the root directory of the configuration repository, run the following command:

    ansible-playbook \
        --ask-become-pass \
        -i inventory.yaml \
        -e @../secrets.yaml \
        -e run_update_script=yes \
        datalibrary.yaml

Each step of the installation will be printed to the terminal. At a site with a fast connection to the internet, the playbook generally finishes within ten minutes, but if bandwidth is limited it may take a few hours, as the installation process involves downloading several GB of software packages and container images.

You should now be able to visit your Data Library server in a browser, but the maprooms are not yet functional because the data that underlies them has yet to be installed.

## Install datasets
Among other things, the ansible playbook has created structures (directories, groups, a database, and permissions) to support the installation of datasets. You can now install your data as described in {ref}`installing-data`. A member of the IRI staff will typically be involved in this process, as it may involve copying large amounts of data from an IRI server to yours.

## Use your new Data Library
You should now be able to visit your Data Library server in a browser. For next steps, see the {doc}`maintenance` page of the current guide, particularly the section {ref}`debugging`, and the {doc}`/user/index`.
