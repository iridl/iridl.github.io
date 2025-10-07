# Data Library Upgrade Installation

This section provides instructions for upgrading Data Library software previously installed on a 
CentOS Stream 7 server to a server configured with CentOS Stream 9 or 10.  Make sure your current 
dlconfig repository is pushed to the server and your secrets.yaml file is saved.  It is important to 
back up the entire system before upgrading your server.

## Make a backup

Make sure you back up the following:

* `/home`
* `/data`
* a dump of the postgres database

```{seealso}
To install CentOS Stream 10 on the server, please reference {doc}`the Server 
Installation pages <../server/index>`.
```

Installation of the Data Library software is automated using 
[ansible](https://docs.ansible.com/ansible_community.html), a configuration
management tool which uses python3.12. If you followed the OS installation process described in {ref}`Install python3.12, git and ansible`, python and ansible should already be installed in `/opt/datalib_venv3.12`.

## Configure the upgraded Data Library

  ```{seealso}
  See {ref}`Configuring git` to set up your account to work with git.
  ```

### Clone your old Data Library configuration

You should already have a DL configuration in bitbucket.  Download that configuration
here. The following is an example, as your repository name will be different.

  ```
  git clone git@bitbucket.org:iridl/dlconfig_myconfig.git dlconfig
  cd dlconfig
  ```

### Upgrade the ansible_collections

Since you are installing a new version of the ansible galaxy collection, delete the old one before installing the new.

  ```
  git rm -rf ansible_collections
  git commit -m "removing old ansible_collections to upgrade CentOS Stream"

  source /opt/datalib_venv3.12/bin/activate
  ansible-galaxy collection install -p . \
      git+https://github.com/iridl/iridl-ansible.git
  ```

* The previous commands should have downloaded the collection to a subdirectory
  called `ansible_collections`. Add that directory to your git repository, 
  and commit the changes.

  ```
  git add ansible_collections
  git commit -m "add new CentOS Stream iridl ansible collection"
  ```

* Copy template configuration files from the collection to the top level of the
  repository.  The `-b` flag will make a backup of your current files:

  ```
  cp -b ansible_collections/iridl/iridl/example/* .
  ```

* Restore your original `playbook.yaml` and `secrets.yaml` files.

  ```
  mv playbook.yaml~ playbook.yaml
  rm secrets.yaml
  ```

  Copy your original `secrets.yaml` file (which is not in the git repository) to the directory above `dlconfig`.

  ```{seealso}
  The `secrets.yaml` file contains the deployment access key to access the repositories defined in your playbook.yaml
  file.  Ansible installs the key in a place that is accessible to content authors so they can deploy content updates. See {ref}`Access key for deployment`.
  ```

* Commit any new customizations and push them to your git server for safe keeping;
  back up `secrets.yaml` by other means, such as copying it to another machine.
* 
  ```
  git commit -m "updates for new version of CentOS"
  git push
  ```

```{note}
  * Never edit the contents of the `ansible_collections` directory. All 
  customization should be made in the configuration files that you copied 
  from the template. In the future when it comes time to upgrade to a newer 
  version of the DL software, you will run the `ansible-galaxy` command 
  again and commit the new version to your configuration repository.

  * Don't upgrade without checking https://github.com/iridl/iridl-ansible for release notes first, because in some
  cases an upgrade may require manual migration steps. (At this writing, 
  there are no upgrade release notes.)
```

From here, you can continue with the New Installation section, starting with {ref}`Run the ansible playbook`.
