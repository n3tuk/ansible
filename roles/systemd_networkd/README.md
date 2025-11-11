# n3t.uk systemd-networkd Ansible Role

This role configures the networking for the system using `systemd-networkd`,
with support for a singe ethernet interface, configured with the IP settings
provided by cloud-init during the bootstrapping process, or as currently
configured.

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
- name: Configure networking via systemd-networkd
  hosts: all
  remote_user: root
  roles:
    - role: systemd_networkd
```
