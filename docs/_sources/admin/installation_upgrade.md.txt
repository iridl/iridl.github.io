# Data Library Upgrade Installation

This section provides instructions for upgrading Data Library software previously installed on a 
CentOS Stream 7 server to a server configured with CentOS Stream 9 or 10.  Make sure your current 
dlconfig repository is pushed to the server and your secrets.yaml file is saved.  It is important to 
back up the entire system before upgrading your server.

## Make a backup

Make sure you back up the following:

* `/home`
* `/data`

## Install the new operating system

```{seealso}
To install CentOS Stream 10 on the server, please reference {doc}`centos_stream`.
```

## Restore from backup
Restore `/home` and `/data` from backups.

## Configure the upgraded Data Library

Installation of the Data Library software is automated using 
[ansible](https://docs.ansible.com/ansible_community.html), a configuration
management tool which uses Python 3.12. If you followed the OS installation process described in {ref}`Install python3.12, git and ansible`, python and ansible are installed in `/opt/datalib_venv3.12`.


### Pull the latest version of your old Data Library configuration

Having restored `/home` from backup, you should have a clone of your `dlconfig` repository in your home directory. Run `git pull` in that repository to be certain that your local copy is up to date.

  ```
  cd ~/dlconfig
  git pull
  ```

### Upgrade ansible collection

Delete the old version of the ansible galaxy collection, and then install the new one:

  ```
  git rm -rf ansible_collections
  git commit -m "removing old ansible_collections to upgrade CentOS Stream"

  source /opt/datalib_venv3.12/bin/activate
  ansible-galaxy collection install -p . \
      git+https://github.com/iridl/iridl-ansible.git
  ```

Copy new versions of these three configuration files from the
collection to the top level of the repository:

  ```
  cp ansible_collections/iridl/iridl/example/ansible.cfg .
  cp ansible_collections/iridl/iridl/example/inventory.cfg .
  cp ansible_collections/iridl/iridl/example/run-ansible .
  ```

Commit the new collection and configuration files to your git
repository, and commit the changes.

  ```
  git add ansible_collections ansible.cfg inventory.cfg run-ansible
  git commit -m "add new CentOS Stream iridl ansible collection"
  ```

Push your changes to your git server for safe keeping; back up
`secrets.yaml` by other means, such as copying it to another machine.

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

  * Before upgrading, check [https://github.com/iridl/iridl-ansible](https://github.com/iridl/iridl-ansible) for release notes, because in some
  cases an upgrade may require manual migration steps. (At this writing, 
  there are no upgrade release notes.)
```

From here, you can continue as for a new installation, starting with {ref}`Run the ansible playbook`.
