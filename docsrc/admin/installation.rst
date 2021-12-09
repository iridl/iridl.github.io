=======================
 Software installation
=======================

The Data Library (DL) software consists of a number of different components (see :ref:`architecture`), each of which has many configuration options. We provide a tool that automates much of the installation process, based on `ansible <https://github.com/ansible/ansible>`_, a server configuration tool.

Ansible uses an "configuration as code" approach, meaning that the configuration of a server is described in files that are kept in a version control system such as git, and ansible automatically ensures that one or more servers are configured in accordance with that desired configuration. This is in contrast with the traditional approach to system administration, where an administrator changes a server's configuration by logging in and typing commands such as `yum install` or `systemctl enable`. Ansible can be used both for the initial installation of the DL software, and for ongoing system administration and upgrades. Advantages of the "configuration as code" approach include:

* Convenience: since many steps of the installation process are automated, it takes less time to install the software than if those steps had to be performed by hand.
* Consistency: automation reduces the likelihood of typos and missed steps, making it more likely that something that works in one installation will also work for others.
* Repeatability: having relevant system configuration details documented in a version control repository makes it easier to reproduce the same configuration on another server, e.g. after a hardware failure or upgrade.

