# Configuring git

We currently use [bitbucket.org](https://bitbucket.org) as our git cloud repository.

The person installing and maintaining the Data Library should already have an account or should [create an account](https://id.atlassian.com/signup?application=bitbucket&continue=https%3A%2F%2Fbitbucket.org%2Faccount%2Fsignin%2F%3Fnext%3D%252F%26redirectCount%3D1) with [https://bitbucket.org](https://bitbucket.org).
Each person maintaining the Data Library should have their own account and follow these instructions individually.

Once you have an account, inform the [Data Library staff](mailto:help@iri.columbia.edu) to grant you access to the repositories.

## Configure your DL Account

* Create a new SSH Key on the Data Library Server

      ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/id_bitbucket

  _This will create an ssh key in ~/.ssh/id_bitbucket. Set a passphrase for your key that you will use to communicate with bitbucket.
  It can be the same as your bitbucket or login password._

* Add the key to your ~/.ssh/config file

      vi ~/.ssh/config

  Add these lines to the file and save it.

      Host bitbucket.org
        AddKeysToAgent yes
        IdentityFile ~/.ssh/id_bitbucket

## Configure Bitbucket

* Provide the details to Bitbucket.org
    * Login to your bitbucket.org account
    * Select the **Settings** gear icon in the upper right of the browser window and select **Personal Bitbucket Settings**.
    * Under **Security**, select **SSH Keys**
    * Select **Add Key**
    * In the Add SSH key dialog, provide a Label to help you identify which key you are adding. For example, you could use
      the account name of the user on the server you're setting the key up for.
    * Copy the contents of ~/.ssh/id_bitbucket.pub to the **Key** field of the **Add SSH key** dialog.
    * Select **Add Key**  
      if it fails, check that you copied the contents properly.  It should look something like:

          ssh-ed25529 LLoWYaPswHzVqQ7L7B07LzIJbntgmHqrE40t17nGXL71QX9IoFGKYoF5pJKUMvR+DZotTm user@example.com

* Make sure the key works. On the Data Library server,

        ssh -T git@bitbucket.org

  It should return:

        authenticated via ssh key

        You can use git to connect to Bitbucket. Shell access is disabled

## Deployment Keys

In addition to your bitbucket account, you will need to grant access to ansible to access the maproom and catalog 
repositories. There are two keys in `secrets.yaml` that need to be defined, but they can both use the same key:

1. bitbucket_private_key for the dlentries and classic maprooms.
2. python_maproom_private_key for the python maprooms.

To create a personal deployment key:

    ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/datalibrary_private_key

Leave the passphrase blank.

### Add the Access or Deploy Key to Bitbucket or Github
In the following, the _xxx_ is your repository code.  For example: _kmd_, _nimet_, _madagascar_, etc.

Install this key into each repository you need ansible to access.  For example,
* dlentries_xxx
* maproom_xxx
* python_maproom_xxx (this one is optional and depends on if you have a python maproom)

You should know where these repositories are, but you can also find them in your playbook.yaml file.

For each repository, add your access key (bitbucket) or deploy key (github)

#### Bitbucket

Go to the repository page and go to repository settings on the left tab.

  * Select Access keys on the left menu, and select Add key.
    * For the Label, enter a descriptive name for this, such as dlentries key for _YourName_
    * Copy the contents of ~/.ssh/datalibrary_private_key.pub to the Key value
    * Select Add SSH Key

#### Github

Go the Settings tab on the top menu

  * Select Deploy keys from the left menu and select Add Deploy Key
    * For the Title, enter a descriptive name for this, such as dlentries key for _YourName_
    * Copy the contents of ~/.ssh/datalibrary_private_key.pub to the Key value
    * ***Do not select Allow Write Access***
