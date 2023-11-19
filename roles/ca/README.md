# n3t.uk Root Certificate Authority Ansible Role

An Ansible role for the installation of the n3t.uk Root Certificate Authority
certificate into the system store to allow all standard utilities to trust any
service which have been configured with a certificate derived from this or any
intermediate authority.

## Requirements

None other than the Ansible Role.

## Bootstrap Support

This role provides support for the bootstrapping process used to install Arch
Linux via the LiveUSB installation image to a working server, via either
physical or virtual hosts.

The `bootstrap_mount_base` variable is checked and if a directory is provided
(i.e. `/mnt`), then the role will deposit the files under that directory, or run
the command via `arch-chroot` into the new installation.

## Role Variables

| Variable               | Default | Description                                                                                                                            |
| :--------------------- | :------ | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base` | `""`    | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install the n3t.uk Root Certificate Authority
  hosts: all
  remote_user: root
  vars:
    bootstrap_mount_base: /mnt
  roles:
    - role: ca
```
