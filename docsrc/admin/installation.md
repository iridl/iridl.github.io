# Data Library Installation

This section provides instructions for installing the Data Library software on a server configure with CentOS Stream 9.

```{seealso}
To install CentOS Stream 9 on the server, please reference {doc}`the server pages <../server/index>`.
```

Installation of the Data Library software is automated using [ansible](https://docs.ansible.com/ansible_community.html),
a configuration management tool which uses python3.12.  A virtual environment for 
this is installed in /opt/datalib_venv3.12

## Configure your Data Library repository

  ```{seealso}
  See {ref}`Configuring git` to set up your account to work with git.
  ```

* Create a configuration repository for your account. This will be used to hold and store all your data library
  customizations.
  These will be put into git and shared with any other team members responsible for the DL installations and updates.

      mkdir dlconfig
      cd dlconfig
      git init

* Inside this new git repository, install the IRIDL ansible collection and dependencies:

      source /opt/datalib_venv3.12/bin/activate
      ansible-galaxy collection install -p . \
          git+https://github.com/iridl/iridl-ansible.git

* The previous command should have downloaded the collection to a subdirectory called `ansible_collections`. Add that
  directory to your git repository, and commit the changes.

      git add ansible_collections
      git commit -m "add iridl ansible collection"

* Copy template configuration files from the collection to the top level of the repository:

      cp ansible_collections/iridl/iridl/example/* .

* Modify `playbook.yaml` and `secrets.yaml` to customize them to the specifics of your site. The files you copied
  contain example configuration values that should be replaced with real email addresses, usernames, *etc.* The
  files include comments that explain the purpose of each configuration option. If you are not ready to set up your real
  Data Library server but merely want to practice the installation process,  *e.g.* in a virtual machine, you can use
  the example files without modification.

```{seealso}
The secrets.yaml file contains the deploy keys (or access keys) to access the repositories defined in your playbook.yaml
file.  These are needed because Ansible runs as root, so it needs it's own access to the repositories.  For that reason,
we want to make sure it only has read access.  See {ref}`Deployment Keys`
```

* Move `secrets.yaml` out of the git repository. For security reasons, unencrypted secrets should not be committed to
  version control.

      mv secrets.yaml ..

* Commit your customizations and push them to your git server for safe keeping; back up `secrets.yaml` by other means,
  such as copying it to another machine.

      git add inventories.cfg playbook.yaml
      git commit -m "add inventoriess and playbook"

```{note}
  * Never edit the contents of the `ansible_collections` directory. All customization should be made in the configuration
  files that you copied from the template. In the future when it comes time to upgrade to a newer version of the DL 
  software, you will run the `ansible-galaxy` command again and commit the new version to your configuration repository.

  * Don't upgrade without checking the release notes first, because in some cases an upgrade may require manual migration
  steps. (At this writing, there are no upgrade release notes because this is the playbook's initial release.)
```

## Run the ansible playbook

Now we are ready to run the playbook, which will download, configure, and install the Data Library software using the
parameters you defined in the configuration files.  We have created a convenience script, `install.bash`, to 
perform this.

From the root directory of the configuration repository, run the following command:

    ./run_ansible --build

It will prompt you for a password, which will be the password of the user you are logged in as.

    BECOME password:

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
