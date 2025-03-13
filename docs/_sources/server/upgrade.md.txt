# Upgrade

This is an overview on the upgrade process to CentOS Stream 9.  There is no recommended way to upgrade from
a previous version of CentOS to CentOS Stream 9.  You must install a fresh installation of CentOS 9 Stream and install
the packages and software you will need.

If you have been using your current Data Library server for other purposes, which is not recommended, you should try to 
make an inventory of the purposes you use this server for.  This will help you determine what needs to be installed when
you install CentOS Stream 9.

## Create Inventory
These instructions can help you get an inventory of what is running on your current server.  This assumes you have a
typical linux server.  Become root first.  Either login as root, or use sudo to become root.

    sudo su - root
    rpm -qa | sort | tee rpm_inventory.txt
    ls -l /usr/local /usr/local/bin | tee local_inventory.txt
    ls -l /opt | tee opt_inventory.txt

    for user in `ls -1 /var/spool/cron`; do
        echo $user >> crontab_inventory.txt;
        crontab -l $user >> crontab_inventory.txt;
        echo "";
    done

    df -hl | tee disk_inventory.txt
    echo "/usr/local/datalib: " | tee -a disk_inventory.txt
    df -h /usr/local/datalib | tee -a disk_inventory.txt
    echo "/home: " | tee -a disk_inventory.txt
    df -h /home | tee -a disk_inventory.txt

    du -sh /home/* | tee user_inventory.txt

    tar cvf inventory.tar rpm_inventory.txt local_inventory.txt opt_inventory.txt crontab_inventory.txt \
    disk_inventory.txt user_inventory.txt -C /etc passwd group postfix aliases yum.repos.d

Inventory files:
* rpm_inventory will be a complete list of all the packages you currently have installed, with their current release version.
* local_inventory will be an inventory of additional software you have installed on the server
* opt_inventory will be another inventory of additional software you have installed on the server
* crontab_inventory will be a list of all the cronjobs running on the server
* disk_inventory will tell you what partitions you currently have mounted and how much space is remaining on each disk.
  This is important to determine how to format your hard drive[s] in preparation for the new installation. Take particular
  note of how much space your home drive is using.
* user_inventory will give you a list of users and how much disk space each is using.

The tarfile _inventory.tar_ will include all these files as well as a backup of your password and group files so you
can recreate their accounts with the correct user ids and place them in the same groups as before. It will also retain
your host email configuration, if your host is configured for email.

Find out where your Data Library data is stored.  It is usually somewhere like /localdisk, /local, /data/, etc.  This is
where you keep all the data that you serve from your data library.  You will want to make sure it is completely backed up
to an external drive before continuing.  Ideally, on 2 separate external drives.

Once you know 