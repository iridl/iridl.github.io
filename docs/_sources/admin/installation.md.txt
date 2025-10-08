# Data Library New Installation

This section provides instructions for installing a new Data Library software on a
server configured with CentOS Stream 9 or 10.

```{seealso}
To install CentOS Stream on the server, please reference {doc}`the Server 
Installation pages <centos_stream>`.
```

Installation of the Data Library software is automated using 
[ansible](https://docs.ansible.com/ansible_community.html), a configuration
management tool which uses python3.12. A virtual python environment for this
should already be installed in `/opt/datalib_venv3.12`, if the server 
installation process was followed. 
See {ref}`Install python3.12, git and ansible`.

## Configure git

We currently use [bitbucket.org](https://bitbucket.org) as our git cloud repository.

The person installing and maintaining the Data Library should already have an account or should [create an account](https://id.atlassian.com/signup?application=bitbucket&continue=https%3A%2F%2Fbitbucket.org%2Faccount%2Fsignin%2F%3Fnext%3D%252F%26redirectCount%3D1)
on [https://bitbucket.org](https://bitbucket.org). Each person maintaining the Data Library should have their own account and
follow these instructions individually.

Once you have an account, inform the [Data Library staff](mailto:help@iri.columbia.edu) to grant you access to the repositories.

### Create the administrator's personal key pair

* Create a new SSH Key on the Data Library Server

    ```
    ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/id_bitbucket
    ```

  This will create an ssh key in `~/.ssh/id_bitbucket` that you will
  use to communicate with Bitbucket. Set a passphrase for your key. It
  can be the same as your Bitbucket or login password.

* Add the key to your `~/.ssh/config` file by adding these lines to the file:

    ```
    Host bitbucket.org
    AddKeysToAgent yes
    IdentityFile ~/.ssh/id_bitbucket
    ```

### Add the administrator's public key to Bitbucket

* Add your key to bitbucket.org
    * Login to your bitbucket.org account
    * Select the `Settings` gear icon in the upper right of the browser window
      and select `Personal Bitbucket Settings`.
    * Under `Security`, select `SSH Keys`
    * Select `Add Key`
    * In the `Add SSH key` dialog, provide a Label to help you identify which key
      you are adding. For example, you could use
      the account name of the user on the server you're setting the key up for.
    * Copy the contents of `~/.ssh/id_bitbucket.pub` to the `Key` field of the 
      `Add SSH key` dialog.
    * Select `Add Key`  
      if it fails, check that you copied the contents properly. It should look
      something like:
  
        ```
        ssh-ed25529 LLoWYaPswHzVqQ7L7B07LzIJbntgmHqrE40t17nGXL71QX9IoFGKYoF5pJKUMvR+DZotTm user@example.com
        ```
* Confirm that the key works: on the Data Library server,

    ```
    ssh -T git@bitbucket.org
    ```

  It should return:

    ```
    authenticated via ssh key

    You can use git to connect to Bitbucket. Shell access is disabled
    ```

### Create access key for deployment

The key we configured in the previous steps will be used by you, the
system administrator, to make changes to the system configuration
repository. We will now create a second key pair that will be used by
non-administrators to deploy content updates from the data catalog and maproom repositories.

To create an access key:

```
ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/datag_access_key
```

Leave the passphrase blank.

### Add deployment access key to Bitbucket

In the following, the _\_xxx_ is your country-specific suffix. For example: `kmd`, `nimet`, `madagascar`, *etc*.

Install this key into each repository you need ansible to access. For example,

* `dlentries_xxx`
* `maproom_xxx`
* `python_maproom_xxx` (this one is optional and depends on if you have a python maproom)

You should know where these repositories are, but you can also find them in your playbook.yaml file.

For each repository, add the access key by navigating to `Repository settings` &rarr; `Access keys`, clicking the `Add key` button, and filling out the form as you did for the administrator's key, but pasting the contents of `~/.ssh/datag_access_key.pub` into the `Key` field.



## Configure the Data Library

* Create a configuration repository for your account. This will be used to hold
  and store all your data library customizations. These will be put into git 
  and shared with any other team members responsible for the DL installations
  and updates.

  ```
  mkdir dlconfig
  cd dlconfig
  git init
  ```

* Inside this new git repository, install the IRIDL ansible collection and
  dependencies.

  ```
  source /opt/datalib_venv3.12/bin/activate
  ansible-galaxy collection install -p . \
      git+https://github.com/iridl/iridl-ansible.git
  ```

* The previous command should have downloaded the collection to a subdirectory
  called `ansible_collections`. Add that directory to your git repository, 
  and commit the changes.

```
  git add ansible_collections
  git commit -m "add iridl ansible collection"
  ```

* Copy template configuration files from the collection to the top level of the
  repository:

  ```
  cp ansible_collections/iridl/iridl/example/* .
  ```

* Modify `playbook.yaml` and `secrets.yaml` to customize them to the specifics
  of your site. The files you copied contain example configuration values that
  should be replaced with real email addresses, usernames, *etc*. The files
  include comments that explain the purpose of each configuration option. If you
  are not ready to set up your real Data Library server but merely want to 
  practice the installation process, _e.g._ in a virtual machine, you can use
  the example files without modification.

  ```{seealso}
  The example ssh key in `secrets.yaml` should be replaced with the deployment access key you created in {ref}`Create access key for deployment`.
  ```

* Move `secrets.yaml` out of the git repository. For security reasons,
  unencrypted secrets should not be committed to version control.

  ```
  mv secrets.yaml ..
  ```

* Commit your customizations and push them to your git server for safe keeping;
  back up `secrets.yaml` by other means, such as copying it to another machine.

  ```
  git add inventory.cfg playbook.yaml
  git commit -m "add inventory and playbook"
  ```

```{note}
  Never edit the contents of the `ansible_collections` directory. All 
  customization should be made in the configuration files that you copied 
  from the template. In the future when it comes time to upgrade to a newer 
  version of the DL software, you will run the `ansible-galaxy` command 
  again and commit the new version to your configuration repository.
```

## Run the ansible playbook

Now you are ready to run the playbook, which will download, configure, and
install the Data Library software using the parameters you defined in the 
configuration files. We have created a convenience script, `run-ansible`, 
for this purpose.

From the root directory of the configuration repository, run the following
command

```
./run-ansible --build
```

It will prompt you for a password, which will be the password of the user you
are logged in as, assuming you have sudo privileges.

```
BECOME password:
```

Each step of the installation will be printed to the terminal. At a site with a
fast connection to the internet, the playbook generally finishes within ten 
minutes, but if bandwidth is limited it may take a few hours, as the
installation process involves downloading several GB of software packages and
container images.

You should now be able to visit your Data Library server in a browser, but the
maprooms are not yet functional because the data that underlies them has yet 
to be installed.

Any flag you can pass to `ansible-playbook` can also be passed to the
`run-ansible` script. For example, if there are errors or failures,
you can pass `-vvv` to the run-ansible command to get more information
about why it is failing.

```
./run-ansible -vvv
```

If you want to run it in `check` mode, pass the flag `--check` to run in 
test mode without changing the Data Library.

```
./run-ansible --check
```

## Install datasets

Among other things, the ansible playbook creates structures (directories,
groups, a database, and permissions) to support the installation of datasets.
You can now install your data as described in {ref}`installing-data`. A 
member of the IRI staff will typically be involved in this process, as it 
may involve copying large amounts of data from an IRI server to yours.

## Use your new Data Library

You should now be able to visit your Data Library server in a browser. For next
steps, see the {doc}`maintenance` page of the current guide, and the 
{doc}`/user/index`.
