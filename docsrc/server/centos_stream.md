(install_centos9)=

# Install CentOS Stream 9 or CentOS Stream 10

This section provides instructions for installing the CentOS Stream 9 for use by the Data Library. If you are 
from an older server, make sure you have a full backup of your current system before following
these instructions, which will completely erase the drive you are installing on. 

## Prepare the server

### Install the  Operating System

* Install CentOS Stream from https://www.centos.org/download.  Select the tab for 9 or 10, then download
  the [x86_64 Architecture ISO](https://mirrors.centos.org/mirrorlist?path=/9-stream/BaseOS/x86_64/iso/CentOS-Stream-9-latest-x86_64-dvd1.iso&redirect=1&protocol=https). We currently only support x86_64.
    * To create a bootable CD of the Installation ISO,
      follow [these instructions](https://docs.centos.org/en-US/centos/install-guide/Making_Media/).
    * **Installation Notes**
        * Select _Server with GUI_ as your Software Selection to make it easier to maintain.
        * When configuring your disk partitions (Installation Destination), Select Customize Storage Configuration.
            * Use LVM when creating the filesystems. This is the default.
                * Don't modify /boot or /boot/efi
            * Increase root partition to 100GB
            * Create a /data partition large enough to hold all your data now
              and in the future.
            * Create a /home partition large enough to house your users' data.
            * Leave 10% of the disk unallocated. This will allow you to create
              snapshots or increase the size of partitions later if necessary.
        * Configure the Network and Hostname (and DNS) appropriately for your network.
        * Set the appropriate Time and Date.
        * Create a Secure Root Password
        * **Create a user account for a system administrator**.  
          Under the Advanced configuration, make the user a member of the `wheel` group so they will be able to
          perform commands as root using `sudo`.

### Post Operating System Installation

Once the server boots up after the installation, you can install the requirements necessary for 
installing the Data Library Software. You must have **sudo** privileges on your account.

#### Update Packages

You should install the latest updates to the Operating System before continuing.

      sudo dnf -y update

#### Disable SELinux

      sudo sed -i s/SELINUX=enforcing/SELINUX=permissive/ /etc/selinux/config 
      sudo setenforce permissive

#### Install python3.12, git and ansible

      sudo dnf install -y git python3.12
      sudo python3.12 -m venv /opt/datalib_venv3.12
      sudo /opt/datalib_venv3.12/bin/pip install --upgrade pip
      sudo /opt/datalib_venv3.12/bin/pip install ansible==11.3.0 requests==2.32.3

#### Reboot

  If any changes were installed during the package updates, reboot now.

      sudo shutdown -r now

Now you can {doc}`Install <../admin/installation>` the Data Library