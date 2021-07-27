## Ansible Roles - Automatic Updates

<p align="center">
  <a href="https://github.com/hubvu/automatic-updates-ansible/actions" alt="Ansible Lint">
    <img src="https://github.com/hubvu/automatic-updates-ansible/actions/workflows/ansible-lint.yml/badge.svg?branch=master">
  </a>
  <a href="https://github.com/hubvu/automatic-updates-ansible/releases" alt="GitHub Release">
    <img src="https://img.shields.io/github/v/release/hubvu/automatic-updates-ansible.svg">
  </a>
  <a href="./LICENSE.md" alt="MIT License">
    <img src="https://img.shields.io/badge/license-MIT-green.svg">
  </a>
  <a href="https://github.com/hubvu/automatic-updates-ansible/tree/main#supported-distributions" alt="Supported Distributions">
    <img src="https://img.shields.io/badge/platform-debian%20%7C%20centos-lightgrey.svg">
  </a>
  <a href="https://img.shields.io/github/repo-size/hubvu/automatic-updates-ansible.svg" alt="Repository Size">
    <img src="https://img.shields.io/github/repo-size/hubvu/automatic-updates-ansible.svg">
  </a>
  <a href="https://img.shields.io/github/directory-file-count/hubvu/automatic-updates-ansible.svg" alt="Repository File Count">
    <img src="https://img.shields.io/github/directory-file-count/hubvu/automatic-updates-ansible.svg">
  </a>
</p>

- [Ansible Roles - Automatic Updates](#ansible-roles---automatic-updates)
  - [What is this?](#what-is-this)
  - [Resource Requirements](#resource-requirements)
  - [Dependencies](#dependencies)
  - [Supported Distributions](#supported-distributions)
  - [Quick-start & Usage](#quick-start--usage)
  - [Contributing](#contributing)
  - [Acknowledgements](#acknowledgements)
  - [License](#license)

### What is this?

* A set of Ansible roles for CentOS and Debian hosts that provides users with the option to enable and/or remove automatic updates for packages installed.
  * **Deploy** - [`centos_automatic_updates.yaml`](./centos_automatic_updates.yaml) and [`debian_automatic_updates.yaml`](./debian_automatic_updates.yaml) playbooks will install packages that enable automatic updates. Custom configuration files to set automatic updates will then be copied to the remote hosts. If there are any existing configuration files, a task will fetch the files and store them in a `backups` directory under the `files` directory.
  * **Remove** - [`centos_automatic_updates_remove.yaml`](./centos_automatic_updates_remove.yaml) and [`debian_automatic_updates_remove.yaml`](./debian_automatic_updates_remove.yaml) will remove packages and configuration files from the hosts that enable automatic updates.
* For reference, below is a demonstration of how the directory structure of `backups` will look like after a number of `debian_automatic_updates.yaml` and `centos_automatic_updates.yaml` playbook runs at different time intervals.

```yaml
# CentOS
.
└── <INVENTORY_HOSTNAME_001>
    ├── 2021-07-25T22:34:13Z-CentOS-8.4
    │   └── automatic.conf
    ├── 2021-07-25T22:35:33Z-CentOS-8.4
    │   └── automatic.conf
    └── 2021-07-25T22:37:20Z-CentOS-8.4
        └── automatic.conf

# Debian
.
└── <INVENTORY_HOSTNAME_002>
    ├── 2021-07-25T22:34:21Z-Debian-10
    │   ├── 20auto-upgrades
    │   ├── 50unattended-upgrades
    │   └── listchanges.conf
    ├── 2021-07-25T22:35:42Z-Debian-10
    │   ├── 20auto-upgrades
    │   ├── 50unattended-upgrades
    │   └── listchanges.conf
    └── 2021-07-25T22:37:27Z-Debian-10
        ├── 20auto-upgrades
        ├── 50unattended-upgrades
        └── listchanges.conf
```

### Resource Requirements

* [Debian](https://www.debian.org/distrib/) and/or [CentOS Stream](https://www.centos.org/centos-stream/) host(s) that the playbooks will be run against.

### Dependencies

* `dnf-automatic`, `unattended-upgrades`, `apt-listchanges`
* [`ansible-vault`](https://docs.ansible.com/ansible/latest/user_guide/vault.html) - [**optional**] - can be used in the [`debian_ssh.yaml`](./debian_ssh.yaml) or [`centos_ssh.yaml`](./centos_ssh.yaml) playbook to encrypt and store sensitive data "at rest". 
  * In this use case, the `ansible_sudo_password` variable, which is used as the privilege escalation password, is stored in a vault.
  * Once the secret has been created and added to the playbook, in order for a user be able to become `sudo` to run the playbook, they will need to decrypt the vault to access the variable.
  * This can be achieved by passing one of the following flags listed below when executing the the playbook;
    * `--ask-vault-pass` 
    * `--vault-password-file`
  * Below is a demonstration of how the encrypted variable is defined in the playbooks;

```yaml
---
# playbook for the debian_automatic_updates role.
- hosts: debian_hosts
  # vars_files:
  #   - become-secret
  become: true
  roles:
    - debian_automatic_updates
```

  * For more information on how to create encrypted variables, review the [official `ansible` documentation](https://docs.ansible.com/ansible/latest/user_guide/vault.html#encrypting-individual-variables-with-ansible-vault).

### Supported Distributions

* Tested on;
  * `debian-10` , `centos-8-stream`

### Quick-start & Usage

```bash
# clone the repository
$ git clone git@github.com:hubvu/automatic-updates-ansible.git

# navigate into the directory
$ cd automatic-updates-ansible/

# run the master playbook `site.yaml` with verbosity
# for non Ansible Vault users
$ ansible-playbook site.yaml \
  --inventory-file=hosts \
  --ask-become-pass \
  --verbose

# run the master playbook `site.yaml` with verbosity
# for Ansible Vault users
$ ansible-playbook site_remove.yaml \
  --inventory-file=hosts \
  --ask-vault-pass \
  --verbose

# review the `backups` directory for the configuration files of each host
$ ls /roles/<role_name>/files/backups/<inventory_hostname>/<date_time>-<distribution_name>-<distribution_version>/
```

### Contributing

* Contribution guidelines for this project can be found in the [Contributing](./CONTRIBUTING.md) document.

### Acknowledgements

* [DNF Automatic - Documentation](https://dnf.readthedocs.io/en/latest/automatic.html).
* [Unattended Upgrades - Debian Wiki](https://wiki.debian.org/UnattendedUpgrades).
* [Ansible Lint](https://github.com/ansible-community/ansible-lint).
* [Ansible Lint for GitHub Action](https://github.com/ansible/ansible-lint-action).

### License

* Licenced under the [MIT License](./LICENSE.md).
