# Configuring git

We currently use [bitbucket.org](https://bitbucket.org) as our git cloud repository.

The person installing and maintaining the Data Library should already have an account or should [create an account](https://id.atlassian.com/signup?application=bitbucket&continue=https%3A%2F%2Fbitbucket.org%2Faccount%2Fsignin%2F%3Fnext%3D%252F%26redirectCount%3D1)
on [https://bitbucket.org](https://bitbucket.org). Each person maintaining the Data Library should have their own account and
follow these instructions individually.

Once you have an account, inform the [Data Library staff](mailto:help@iri.columbia.edu) to grant you access to the repositories.

## Create the administrator's personal key pair

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

## Add the administrator's public key to Bitbucket

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

## Access key for deployment

The key we configured in the previous steps will be used by you, the
system administrator, to make changes to the system configuration
repository. We will now create a second key pair that will be used by
non-administrators to deploy content updates from the data catalog and maproom repositories.

To create an access key:

```
ssh-keygen -t ed25519 -b 4096 -f ~/.ssh/datag_access_key
```

Leave the passphrase blank.

### Add the access key to Bitbucket

In the following, the _\_xxx_ is your country-specific suffix. For example: `kmd`, `nimet`, `madagascar`, *etc*.

Install this key into each repository you need ansible to access. For example,

* `dlentries_xxx`
* `maproom_xxx`
* `python_maproom_xxx` (this one is optional and depends on if you have a python maproom)

You should know where these repositories are, but you can also find them in your playbook.yaml file.

For each repository, add the access key by navigating to `Repository settings` &rarr; `Access keys`, clicking the `Add key` button, and filling out the form as you did for the administrator's key, but pasting the contents of `~/.ssh/datag_access_key.pub` into the `Key` field.
