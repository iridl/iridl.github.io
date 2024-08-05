(prerequisites)=

# Prerequisites

This is not a tutorial on linux system administration. We will assume that the
reader is able to perform tasks such as:

* Install a linux distribution on a server.
* Manage storage volumes, *e.g.* configure LVM; configure RAID; format and mount
  filesystems.
* Manage unix users and groups, *e.g.* create a user, set a user's password, add
  a user to a group.
* Manage ssh keys for use with ssh and git.
* Manage containerized services using `docker` and `docker-compose`.
* Create a git repository, commit content to the repository, collaborate with
  others by pushing, pulling and merging git branches.

If you need to acquire these skills, we recommend the following resources:

* [Red Hat System Administration I](https://www.redhat.com/en/services/training/rh124-red-hat-system-administration-i)
* [Red Hat System Administration II](https://www.redhat.com/en/services/training/rh134-red-hat-system-administration-ii)
* [Docker Overview](https://docs.docker.com/get-started/overview/)
* [Get Started with Docker](https://docs.docker.com/guides/get-started/)
* [Pro Git](https://git-scm.com/book/en/v2)
* [Git User Manual](https://git-scm.com/docs/user-manual)

You will be using [ansible](https://docs.ansible.com/ansible_community.html), a
configuration management tool, to install and configure the Data Library
software. This guide does not assume prior familiarity with ansible, but after
working through the installation instructions we do recommend that you read the
ansible documentation.
