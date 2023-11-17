# n3t.uk systemd-resolved Ansible Role

This role configures the DNS resolution for the system using `systemd-resolved`,
with support for direct resolution via [`1.1.1.1`][one-one-one-one], falling
back to [`dns.google`][dns-google] in the event of that service's failure.

[one-one-one-one]: https://one.one.one.one/
[dns-google]: https://dns.google/

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
- name: Configure networking via systemd-resolved
  hosts: all
  remote_user: root
  roles:
    - role: systemd_resolved
```
