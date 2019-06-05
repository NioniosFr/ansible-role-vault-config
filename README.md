Ansible Role: Vault Configuration
=========

A generic role to install a binary application released on GitHub in a Linux x86_64 based distro.

Requirements
------------

The role targets Linux based systems with the `systemd` service manager system.

The role is intended to run on the remote machine, which means that internet connectivity on remote is required.

The role tries to follow [production hardening recommendations](https://learn.hashicorp.com/vault/operations/production-hardening)
as much as possible, while being simple to use for development purposes.

End-to-End TLS and other complex escenarios are infrastructure dependent and ar not in the scope of this role.

Role Variables
--------------

Default:

```yaml
---
vault_bin_dir: "/opt/vault" # The path where the vault binary is located
vault_cfg_dir: "/etc/vault" # The path where the vault configuration will be stored

# https://www.vaultproject.io/docs/configuration/index.html#plugin_directory
vault_var_dir: "/var/vault" # The base variables folder to use with vault

vault_user: "vault" # The user to run vault with
vault_group: "vault" # The group the vault user belongs to

vault_service_role: "server" # server | agent

vault_configs: |
  log_level    = "Info"
  ui           = true

vault_listen: |
  listener "tcp" {
    address     = "127.0.0.1:8200"
    tls_disable = 1
  }

vault_storage: |
  storage "file" {
    path        = "{{ vault_data_dir }}"
  }

vault_seals: ""

vault_telemetry: ""

```

Dependencies
------------

The [vault](https://www.vaultproject.io) binary has to be present on the system.

You may use the (generic) [nioniosfr.hashicorp_app](https://galaxy.ansible.com/nioniosfr/hashicorp_app) role to install vault.

Example Playbook
----------------

Full example to install and configure vault to listen on a custom IP address without TLS (not-recommended).

```yaml
---
- name: Install HashiCorp Vault
  hosts: all
  become: true

  vars:
    vault_bin_dir: "/opt/vault"

  roles:

    - role: nioniosfr.hashicorp_app
      vars:
        hashicorp_app_name: "vault"
        hashicorp_app_version: "1.1.2"
        hashicorp_app_binary_dest: "{{ vault_bin_dir }}"

    - role: ansible-role-vault-config
      vars:
        vault_listen: |
          listener "tcp" {
            address     = "192.168.1.1:8200"
            tls_disable = 1
          }

```

License
-------

MIT

Author Information
------------------

[NioniosFr](https://github.com/NioniosFr)
