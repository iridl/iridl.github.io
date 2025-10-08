(install_centos)=

# Install CentOS Stream

This section provides instructions for installing the Linux CentOS Stream Operating System for use by the Data Library. 
If you are upgrading from an older server, make sure you have a full backup of your current system before following
these instructions, which will completely erase the drive you are installing on.

We recommend CentOS Stream 10.  If your CPU isn't supported by CentOS Stream 10, CentOS Stream 9 will also work. CentOS7 is beyond its end of life and is no longer recommended to install.

```{note}
CentOS Stream 10 requires an AMD/Intel 64-bit x86_64_v3 CPU or higher.  Typically, any CPU less than 10 years old
should support CentOS Stream 10.  To find out for sure: Linux - use the command lscpu;  Windows - use "About This Computer". 
Then search for that CPU in a search engine to find the specifications for that CPU. 
```

## Install the  Operating System

* Install CentOS Stream from https://www.centos.org/download.  Select the tab for 10, then download
  the x86\_64 Architecture ISO. We currently only support x86\_64.
    * To create a bootable USB of the Installation ISO, use [balenaEtcher](https://etcher.balena.io)
    * Installation Notes
        * Select *Server with GUI* as your Software Selection to make it easier to maintain.
        * When configuring your disk partitions (Installation Destination), Select Customize Storage Configuration.
            * Use LVM when creating the filesystems. This is the default.
                * Don't modify /boot or /boot/efi
            * Increase root partition to 100GB
            * Create a /data partition large enough to hold all your
              data now and in the future. Many users put /data on a
              separate disk (or disk array) from the operating system.
            * Create a /home partition large enough to house your users' data.
            * Leave 10% of the disk unallocated. This will allow you to create
              snapshots or increase the size of partitions later if necessary.
        * Configure the Network and Hostname (and DNS) appropriately for your network.
        * Set the appropriate Time and Date.
        * Create a Secure Root Password
        * Create a user account for a system administrator.  
          Under the Advanced configuration section, make the user a member of the `wheel` group so they will be able to
          perform commands as root using `sudo`.

## Post Operating System Installation

Once the server boots up after the installation, you can install the requirements necessary for 
installing the Data Library Software. You must have `sudo` privileges on your account.

### Update Packages

You should install the latest updates to the Operating System before continuing.

```
sudo dnf -y update
```

### Disable SELinux

```
sudo sed -i s/SELINUX=enforcing/SELINUX=permissive/ /etc/selinux/config 
sudo setenforce permissive
```

### Install python3.12, git and ansible

```
sudo dnf install -y git python3.12
sudo python3.12 -m venv /opt/datalib_venv3.12
sudo /opt/datalib_venv3.12/bin/pip install --upgrade pip
sudo /opt/datalib_venv3.12/bin/pip install ansible==11.3.0 requests==2.32.3
```

### Reboot

Reboot the server before continuing.

```
sudo shutdown -r now
```
