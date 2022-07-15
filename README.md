# [lynis](#lynis)

|GitHub|GitLab|
|------|------|
|[![github](https://github.com/mullholland/ansible-role-lynis/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-lynis/actions)|[![gitlab](https://gitlab.com/mullholland/ansible-role-lynis/badges/main/pipeline.svg)](https://gitlab.com/mullholland/ansible-role-lynis)|

Installs lynis and schedules scans.

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
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
lynis_report_from: '{{ ansible_hostname }}@domain.tld'
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


## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
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

The machine needs to be prepared in CI this is done using `molecule/default/prepare.yml`:
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





## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

-   [debian9](https://hub.docker.com/r/mullholland/docker-molecule-debian9)
-   [debian10](https://hub.docker.com/r/mullholland/docker-molecule-debian10)
-   [debian11](https://hub.docker.com/r/mullholland/docker-molecule-debian11)
-   [ubuntu1804](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu1804)
-   [ubuntu2004](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu2004)
-   [ubuntu2204](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu2204)
-   [centos7](https://hub.docker.com/r/mullholland/docker-molecule-centos7)
-   [centos-stream8](https://hub.docker.com/r/mullholland/docker-molecule-centos-stream8)
-   [ubi8](https://hub.docker.com/r/mullholland/docker-molecule-ubi8)
-   [fedora35](https://hub.docker.com/r/mullholland/docker-molecule-fedora35)
-   [fedora36](https://hub.docker.com/r/mullholland/docker-molecule-fedora36)
-   [amazonlinux](https://hub.docker.com/r/mullholland/docker-molecule-amazonlinux)
-   [rockylinux8](https://hub.docker.com/r/mullholland/docker-molecule-rockylinux8)
-   [almalinux8](https://hub.docker.com/r/mullholland/docker-molecule-almalinux8)

The minimum version of Ansible required is 2.10, tests have been done to:

-   The previous versions.
-   The current version.





If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-lynis/issues)

## [License](#license)

MIT


## [Author Information](#author-information)

[Mullholland](https://github.com/mullholland)

## [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)
