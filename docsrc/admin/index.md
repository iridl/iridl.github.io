# System Administrator Guide

```{toctree}

architecture
installation
maintenance
```
This guide provides information to help a system administrator install, configure, and maintain a server running the Data Library software.

## Prerequisites
This is not a tutorial on linux system administration. We will assume that the reader is able to perform tasks such as
* Create a git repository, commit content to the repository, collaborate with others by pushing, pulling and merging git branches.
* Install a linux distribution on a server
* Manage unix users and groups, e.g. create a user, set/reset a user's password, add a user to a group.
* Manage storage volumes, e.g. configure LVM; configure RAID; format and mount filesystems.

You will be using [ansible](https://docs.ansible.com/ansible_community.html), a configuration management tool, to install and configure the Data Library software. We do not assume prior familiarity with ansible--the installation guide includes step-by-step instructions for installing and running it--but after working through the installation instructions we do recommend that you familiarize yourself with the ansible documentation.
