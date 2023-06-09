# [lynis](#lynis)

Installs lynis and schedules scans.

|GitHub|GitLab|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-lynis/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-lynis/actions)|[![gitlab](https://gitlab.com/opensourceunicorn/ansible-role-lynis/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-lynis)|[![quality](https://img.shields.io/ansible/quality/57655)](https://galaxy.ansible.com/mullholland/lynis)|[![downloads](https://img.shields.io/ansible/role/d/57655)](https://galaxy.ansible.com/mullholland/lynis)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-lynis.svg)](https://github.com/mullholland/ansible-role-lynis/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-lynis/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    lynis_report_mail: false  # Dont send mails during testing
  roles:
    - role: "mullholland.lynis"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-lynis/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: Install dependencies
      ansible.builtin.package:
        name:
          - "cronie"  # For the cron script
          - "hostname"  # For the cron script
          - "findutils"  # For lynis scans
          - "passwd"  # For lynis scans
          - "procps-ng"  # For lynis scans
          - "net-tools"  # For lynis scans
          - "iproute"  # For lynis scans
        state: present
      when:
        - ansible_distribution in [ "RedHat", "CentOS", "Amazon", "Rocky", "AlmaLinux", "Fedora" ]

    - name: Install dependencies
      ansible.builtin.apt:
        name:
          - "cron"  # To create crontab entry
          - "hostname"  # For the cron script
        state: present
        update_cache: true
      when:
        - ansible_os_family == "Debian"
```


## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-lynis/blob/master/defaults/main.yml):

```yaml
---
# cron job vars
lynis_cron:
  minute: '5'
  hour: '0'
  day: '*'
  month: '*'
  weekday: '0'
# cron script vars
# send email after scan?
lynis_report_mail: true
lynis_report_from: '"{{ ansible_hostname }} <noreply@domain.tld>"'
lynis_report_to: 'security@domain.tld'
# Days before logs are purged. Defaults to '90'.
lynis_log_expire: '90'

lynis_profile: "/etc/lynis/ansible.prf"
# lynis custom profile vars
# Split in commonly used partes
# default profile
# https://raw.githubusercontent.com/CISOfy/lynis/master/default.prf
# Tests to skip in the audit runs.
lynis_prf_skip_tests: []
#  - "SSH-7408"
# Lynis Enterprise Settings
lynis_prf_enterprise_options:
  - "license_key="
  - "#allow-auto-purge=yes"
  - "#hostid=40-char-hash"
  - "#hostid2=64-char-hash"
  - "license-key="
  - "#proxy-protocol=https"
  - "#proxy-server=10.0.1.250"
  - "#proxy-port=3128"
  - "#system-groups=groupname1,groupname2,groupname3"
  - "compliance-standards=cis,hipaa,iso27001,pci-dss"
  - "#system-customer-name=mycustomer"
  - "upload=no"
  - "upload-server="
  - "upload-options="
  - "#tags=db,production,ssn-1304"
lynis_prf_custom_options:
  - "colors=yes"
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-lynis/blob/master/requirements.txt).


## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-lynis/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/repository/docker/mullholland/docker-centos-systemd/general)|all|
|[Amazon](https://hub.docker.com/repository/docker/mullholland/docker-amazonlinux-systemd/general)|Candidate|
|[Fedora](https://hub.docker.com/repository/docker/mullholland/docker-fedora-systemd/general)|all|
|[Ubuntu](https://hub.docker.com/repository/docker/mullholland/docker-ubuntu-systemd/general)|all|
|[Debian](https://hub.docker.com/repository/docker/mullholland/docker-debian-systemd/general)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-lynis/issues)

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-lynis/blob/master/LICENSE).

## [Author Information](#author-information)

[Mullholland](https://mullholland.net)

Please consider [sponsoring me](https://github.com/sponsors/mullholland).
