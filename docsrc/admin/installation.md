(installation)=

# Installation

This section provides instructions for installing the Data Library software on a server.

Installation of the Data Library software is automated using [ansible](https://docs.ansible.com/ansible_community.html),
a configuration management tool.

## Prepare the server

### Install the CentOS9 Stream Operating System

* Install CentOS 9 Stream from https://www.centos.org/download.  Select the tab for 9, then download
  the [x86_64 Architecture ISO](https://mirrors.centos.org/mirrorlist?path=/9-stream/BaseOS/x86_64/iso/CentOS-Stream-9-latest-x86_64-dvd1.iso&redirect=1&protocol=https). We currently only support x86_64.
    * To create a bootable CD of the Installation ISO,
      follow [these instructions](https://docs.centos.org/en-US/centos/install-guide/Making_Media/).
    * **Installation Notes**
        * Select _Server with GUI_ as your Software Selection to make it easier to maintain.
        * When configuring your disk partitions (Installation Destination), Select Customize Storage Configuration.
            * Use LVM when creating the filesystems. This is the default.
                * Don't modify /boot or /boot/efi
            * Increase root partition to 100GB
            * Create a /data partition large enough to hold all of your data now
              and in the future.
            * Create a /home partition large enough to house your users' data.
            * Leave 10% of the disk unallocated. This will allow you to create
              snapshots or increase the size of partitions later if necessary.
        * Configure the Network and Hostname (and DNS) appropriately for your network.
        * Set the appropriate Time and Date.
        * Create a Secure Root Password
        * Create a user account for the system administrator who will be performing the
          installation. Under the Advanced configuration, make the user a member of the `wheel` group so they will be able to
          perform commands as root using `sudo`.

### Post Installation

Once the server boots back up, you can install the requirements for installing the Data Library Software.

* Bring the server up to the most recent version of CentOS Stream 9

        sudo dnf update -y

* Install git and ansible:

        sudo dnf install -y git ansible-core

* Disable SELinux:

        sudo sed -i s/SELINUX=enforcing/SELINUX=permissive/ /etc/selinux/config
        sudo setenforce permissive

   **Note:**
   If you feel SELinux is important to the security of your server, please start a conversation with us at help@iri.columbia.edu.

#### Configure bitbucket

* Create a new SSH Key

        ssh-keygen -t ed25519 -b 4096 -f ~/id_bitbucket

  _This will create an ssh key for use with bitbucket in ~/.ssh/id_bitbucket. Set a good passphrase different 
   from your password. You will need to use this passphrase every time you need to commit changes to the bitbucket repository,
   so be sure to save it somewhere._

* Add the key to your ~/.ssh/config file

        vi ~/.ssh/config

  Add these lines to the file and save it.

        Host bitbucket.org
           AddKeysToAgent yes
           IdentityFile ~/.ssh/id_bitbucket

* Provide the details to Bitbucket.org
  * Login to your bitbucket.org account
  * Select the *Settings* gear icon in the upper right of the browser window and select **Personal Bitbucket Settings**.
  * Under **Security**, select **SSH Keys**
  * Select **Add Key**
  * In the Add SSH key dialog, provide a Label to help you identify which key you are adding. For example, you could use
    the account name of the user on the server you're setting the key up for.
  * Copy the contents of ~/.ssh/id_bitbucket.pub to the **Key** field of the **Add SSH key** dialog.
  * Select **Add Key**  
    if it fails, check that you copied the contents properly.  It should look something like:  

        ssh-ed25529 LLoWYaPswHzVqQ7L7B07LzIJbntgmHqrE40t17nGXL71QX9IoFGKYoF5pJKUMvR+DZotTm user@example.com

* Make sure the key works. On the server,

        ssh -T git@bitbucket.org

  It should return, "authenticated via ssh key"

#### Configure your Data Library repository

* Create a configuration repository for your installation.  This will be used to hold and store all your customizations.

        mkdir dlconfig
        cd dlconfig
        git init

* Inside the new git repository, install the IRIDL ansible collection and dependencies:

        ansible-galaxy collection install -p . \
            git+https://github.com/iridl/iridl-ansible.git \
            community.docker:3.13.6

* The previous command should have downloaded the collection to a subdirectory
  called `ansible_collections`. Add that directory to your git repository, and commit
  the changes.

        git add ansible_collections
        git commit -m "add iridl ansible collection"

* Copy template configuration files from the collection to the top level of the
  repository:

        cp ansible_collections/iridl/iridl/example/* .

* Move `secrets.yaml` out of the git repository. For security reasons, unencrypted secrets should not be committed to
  version control.

        mv secrets.yaml ..

* Modify `playbook.yaml` and `../secrets.yaml` to customize them to the specifics of your site. The files you copied 
  contain example configuration values that should be replaced with real email addresses, usernames, *etc.* The
  files include comments that explain the purpose of each configuration option. If you are not ready to set up your real
  Data Library server but merely want to practice the installation process,  *e.g.* in a virtual machine, you can use the
  example files without modification.

* Commit your customizations and push them to your git server for safe keeping; back up `secrets.yaml` by other means, 
  such as copying it to another machine.

   #### Notes

  * Never edit the contents of the `ansible_collections` directory. All customization should be made in the configuration
  files that you copied from the template. In the future when it comes time to upgrade to a newer version of the DL 
  software, you will run the `ansible-galaxy` command again and commit the new version to your configuration repository.

  * Don't upgrade without checking the release notes first, because in some cases an upgrade may require manual migration
  steps. (At this writing, there are no upgrade release notes because this is the playbook's initial release.)

## Run the ansible playbook

Now we are ready to run the playbook, which will download, configure, and install the Data Library software using the
parameters you defined in the configuration files.

From the root directory of the configuration repository, run the following command:

    ansible-playbook \
       --ask-become-pass \
       -i inventory.cfg \
       -e @../secrets.yaml \
       -e run_update_script=yes \
       playbook.yaml

It will prompt you for a password, which will be the password of the user you are logged in as.

Each step of the installation will be printed to the terminal. At a site with a fast connection to the internet, the
playbook generally finishes within ten minutes, but if bandwidth is limited it may take a few hours, as the 
installation process involves downloading several GB of software packages and container images.

You should now be able to visit your Data Library server in a browser, but the maprooms are not yet functional because
the data that underlies them has yet to be installed.

## Install datasets

Among other things, the ansible playbook has created structures (directories,
groups, a database, and permissions) to
support the installation of datasets. You can now install your data as described
in {ref}`installing-data`. A member of
the IRI staff will typically be involved in this process, as it may involve
copying large amounts of data from an IRI
server to yours.

## Use your new Data Library

You should now be able to visit your Data Library server in a browser. For next
steps, see the {doc}`maintenance` page
of the current guide, and the {doc}`/user/index`.
