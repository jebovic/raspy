kubeadm
=========

[![Build Status](https://travis-ci.org/jebovic/ansible-kubeadm.svg?branch=master)](https://travis-ci.org/jebovic/ansible-kubeadm) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-jebovic.kubeadm-blue.svg?style=flat)](https://galaxy.ansible.com/jebovic/kubeadm)

Install and configure kubeadm

This role is a part of my [OPS project](https://github.com/jebovic/ops), follow this link to see it in action. OPS provides a lot of stuff, like a vagrant file for development VMs, playbooks for roles orchestration, inventory files, examples for roles configuration, ansible configuration file, and many more.

Dependencies
------------

This role depends on [jebovic.DEP_NAME](https://github.com/jebovic/ansible-DEP_NAME) role to be fully functional

Role Variables
--------------

```yaml
```

Example Playbook
----------------

```yaml
- hosts: servers
  roles:
     - { role: jebovic.kubeadm }
```

Example : config
----------------

```yaml

# kubeadm install configuration
kubeadm_apt_key_url: "https://packages.cloud.google.com/apt/doc/apt-key.gpg"
kubeadm_apt_repositories:
  - deb http://apt.kubernetes.io/ kubernetes-stretch main
kubeadm_packages:
  - kubeadm

```

Tags
----

* kubeadm_config : only update config and restart service

Add slack notifications
-----------------------

```
travis encrypt "jebovic:TRAVIS_TOKEN" --add notifications.slack
```

License
-------

MIT

Author Information
------------------

Jérémy Baumgarth https://github.com/jebovic
