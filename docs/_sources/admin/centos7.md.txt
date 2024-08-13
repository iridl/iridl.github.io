(centos7)=

# CentOS7 Updates

CentOS7 is past the end of its life and no longer getting updates. Until you can
upgrade to CentOS9, these updates will allow you to continue using CentOS7.

## Update your Ansible Playbook

When you installed the Data Library, using CentOS7 procedures, you created a
directory called dlconfig and downloaded the IRIDL Ansible collection there.
See [Create a Configuration Repository](https://dldocs.iri.columbia.edu/admin/installation.html#create-a-configuration-repository).

We are going to update that directory in-place.
```
cd dlconfig
ansible-galaxy collection install \
    -p . \
    git+https://github.com/iridl/iridl-ansible.git \
    community.docker
```

