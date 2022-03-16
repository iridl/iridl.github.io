(installation)=
# Installation
This section provides instructions for installing the Data Library software on a server.

Installation of the Data Library software is automated using [ansible](https://docs.ansible.com/ansible_community.html), a configuration management tool.

## Prepare the server
Before running the installation script, perform the following steps to prepare the server:
* Install CentOS 7 (minimal server configuration). For more information on this choice of operating system, see {ref}`infrastructure`.
* Create a user account for the system administrator who will be performing the installation. Make that user a member of the `wheel` group so that they will be able to perform commands as root using `sudo`.
* Mount a volume with at least 1TB of storage space, preferably with mirror RAID, at `/data`. List the volume in `/etc/fstab` to ensure that it will be mounted at boot time. We recommend using LVM to create logical volumes, and formatting the volume with XFS. Note that an XFS filesystem can be expanded but not shrunk, so it may be preferable to leave some disk space unallocated, to be used for snapshots or unanticipated storage needs, rather than putting all of the available space into the XFS-formatted volume.
* Install git and python3:

        sudo yum install -y git python3

* Install ansible with pip. The `--user` option causes it to be installed in ~/.local, to avoid interfering with python packages provided by CentOS.

        # Note: do not use sudo here.
        python3 -m pip install --user --upgrade pip
        python3 -m pip install --user ansible==4.5.0

* Disable SELinux and reboot:

        sudo sed -i s/SELINUX=enforcing/SELINUX=disabled/ /etc/selinux/config
        sudo shutdown -r now

```{note}
If you feel SELinux is important to the security of your server, please start a conversation with us at help@iri.columbia.edu.
```

## Create a configuration repository
* Log back into the server after rebooting. Create a git repository to track your Data Library configuration. At IRI we call ours `dlconfig`.

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

* Modify `playbook.yaml` and `../secrets.yaml` to customize them to the specifics of your site. The files that you copied contain example configuration values that should be replaced with real email addresses, user names, *etc.* The files include comments that explain the purpose of each configuration option. If you are not ready to set up your real Data Library server but merely want to practice the installation process, *e.g.* in a virtual machine, you can use the example files without modification.

* Commit your customizations and push them to your git server for safe keeping; back up `secrets.yaml` by other means, such as copying it to another machine.

Never edit the contents of the `ansible_collections` directory. All customization should be made in the configuration files that you copied from the template. In the future when it comes time to upgrade to a newer version of the DL software, you will run the `ansible-galaxy` command again and commit the new version to your configuration repository. Don't upgrade without checking the release notes first, because in some cases an upgrade may require manual migration steps. (At this writing, there are no upgrade release notes because this is the playbook's initial release.)

## Run the ansible playbook
Now we are ready to run the playbook, which will download, configure, and install the Data Library software using the parameters you defined in the configuration files.

From the root directory of the configuration repository, run the following command:

    ansible-playbook \
        --ask-become-pass \
        -i inventory.cfg \
        -e @../secrets.yaml \
        -e run_update_script=yes \
        playbook.yaml

Each step of the installation will be printed to the terminal. At a site with a fast connection to the internet, the playbook generally finishes within ten minutes, but if bandwidth is limited it may take a few hours, as the installation process involves downloading several GB of software packages and container images.

You should now be able to visit your Data Library server in a browser, but the maprooms are not yet functional because the data that underlies them has yet to be installed.

## Install datasets
Among other things, the ansible playbook has created structures (directories, groups, a database, and permissions) to support the installation of datasets. You can now install your data as described in {ref}`installing-data`. A member of the IRI staff will typically be involved in this process, as it may involve copying large amounts of data from an IRI server to yours.

## Use your new Data Library
You should now be able to visit your Data Library server in a browser. For next steps, see the {doc}`maintenance` page of the current guide, and the {doc}`/user/index`.
