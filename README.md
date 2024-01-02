# Ansible role [stratis](https://galaxy.ansible.com/ui/standalone/roles/buluma/stratis/documentation)

Install stratis and carves pools and filesystems.

|GitHub|Version|Issues|Pull Requests|Downloads|
|------|-------|------|-------------|---------|
|[![github](https://github.com/buluma/ansible-role-stratis/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-stratis/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-stratis.svg)](https://github.com/buluma/ansible-role-stratis/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-stratis.svg)](https://github.com/buluma/ansible-role-stratis/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-stratis.svg)](https://github.com/buluma/ansible-role-stratis/pulls/)|[![Ansible Role](https://img.shields.io/ansible/role/d/buluma/stratis)](https://galaxy.ansible.com/ui/standalone/roles/buluma/stratis/documentation)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/buluma/ansible-role-stratis/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: buluma.stratis
    # It's not easy to test block-devices in CI. That's why the next block
    # of variables is commented, but left here as an example.
    #    stratis_pools:
    #      - name: my_pool
    #        devices:
    #          - /dev/vdb
    #          - /dev/vdc
    #    stratis_filesystems:
    #      - name: my_filesystem
    #        pool: my_pool
    #    stratis_mounts:
    #      - mountpoint: /mnt/my_mountpoint
    #        device: /stratis/my_pool/my_filesystem
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/buluma/ansible-role-stratis/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: yes
  gather_facts: no

  vars:
    devices:
      - name: vdc
        major: 252
        minor: 2
      - name: vdd
        major: 252
        minor: 3

  roles:
    - role: buluma.bootstrap

  tasks:
    - name: create storage file
      command: dd if=/dev/zero of=/{{ item.name }} bs=1M count=1K
      args:
        creates: "/{{ item.name }}"
      loop: "{{ devices }}"
      notify:
        - create loopback device
        - loopback device to storage file
      loop_control:
        label: "/{{ item.name }}"

  handlers:
    - name: create loopback device
      command: mknod /dev/{{ item.name }} b {{ item.major }} {{ item.minor }}
      loop: "{{ devices }}"
      loop_control:
        label: "/dev/{{ item.name }}"
      changed_when: no

    - name: loopback device to storage file
      command: losetup /dev/{{ item.name }} /{{ item.name }}
      loop: "{{ devices }}"
      failed_when: no
      loop_control:
        label: "/dev/{{ item.name }} to /{{ item.name }}"
      changed_when: no
```

Also see a [full explanation and example](https://buluma.github.io/how-to-use-these-roles.html) on how to use these roles.


## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-stratis/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | Version |
|-------------|--------|--------|
|[buluma.bootstrap](https://galaxy.ansible.com/buluma/bootstrap)|[![Ansible Molecule](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml/badge.svg)](https://github.com/buluma/ansible-role-bootstrap/actions/workflows/molecule.yml)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-bootstrap.svg)](https://github.com/shadowwalker/ansible-role-bootstrap)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.github.io/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-stratis/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/repository/docker/buluma/enterpriselinux/general)|8|
|[Fedora](https://hub.docker.com/repository/docker/buluma/fedora/general)|all|

The minimum version of Ansible required is 2.12, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-stratis/issues)

## [Changelog](#changelog)

[Role History](https://github.com/buluma/ansible-role-stratis/blob/master/CHANGELOG.md)

## [License](#license)

[Apache-2.0](https://github.com/buluma/ansible-role-stratis/blob/master/LICENSE)

## [Author Information](#author-information)

[Shadow Walker](https://buluma.github.io/)

