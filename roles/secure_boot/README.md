# n3t.uk Secure Boot Ansible Role

An Ansible role for the creation of the keys and databases needed to support
Secure Boot on a physical host. This will allow the UEFI stubs for the kernel
and initramfs to be signed, although currently does not install the public keys
into the UEFI for validation.

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
- name: Create and install the Secure Boot keys
  hosts: all
  remote_user: root
  vars:
    bootstrap_mount_base: /mnt
  roles:
    - role: secure_boot
```
