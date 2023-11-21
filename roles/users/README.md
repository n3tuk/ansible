# n3t.uk sudo Ansible Role

An Ansible role for the installation and configuration of users on the system,
as well as configuration of the root user too.

## Requirements

None other than the Ansible Role.

## Bootstrap Support

This role provides support for the bootstrapping process used to install Arch
Linux via the LiveUSB installation image to a working server, via either
physical or virtual hosts.

The `bootstrap_mount_base` variable is checked and if a directory is provided
(i.e. `/mnt`), then the role will deposit the files under that directory, or run
the command via `arch-chroot` into the new installation.

Due to the way that the modules [user][ansible-user], [group][ansible-group],
and [authorized_key][ansible-authorized-key] work, they cannot support operating
within the remote chroot'd environment. As such, when `bootstrap_mount_base` is
set only limited settings are managed. As such, it is recommended that the
`users` role be run both from the `bootstrap` play, and then the
[`baseline`][play-baseline] or [`users`][play-users] plays to ensure they are
fully configured.

[ansible-user]: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html
[ansible-group]: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html
[ansible-authorized-key]: https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html
[play-baseline]: https://github.com/n3tuk/ansible/blob/main/plays/baseline.yaml
[play-users]: https://github.com/n3tuk/ansible/blob/main/plays/upgrade.yaml

## Role Variables

| Variable               | Default | Description                                                                                                                            |
| :--------------------- | :------ | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base` | `""`    | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Create and update users and groups
  hosts: all
  remote_user: root
  vars:
    bootstrap_mount_base: /mnt
  roles:
    - role: users
```
