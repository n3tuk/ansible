# n3t.uk Bootstrap Ansible Role

An Ansible role for the creation and wiping of filesystems, and preparation of
the `/etc/fstab` and `/etc/crypttab{,.initramfs}` files on Arch Linux systems
during initial installation of the physical or virtual machine.

## Requirements

None other than the Ansible Role, although the use of `filesystems` is
recommended in order to prepare the mount points on the filesystem before
installation.

## Role Variables

| Variable                    | Default                                                                            | Description                                                                                                                            |
| :-------------------------- | :--------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_dir`       | `""`                                                                               | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |
| `bootstrap_pacstrap_extras` | `[]`                                                                               | Any additional packages to be installed as part of `pacstrap`.                                                                         |
| `bootstrap_vconsole_keymap` | `uk`                                                                               | The keymap to provide to the virtual console.                                                                                          |
| `bootstrap_locale_lang`     | `en_GB.UTF-8`                                                                      | The default locale used on the system.                                                                                                 |
| `bootstrap_locale_gen`      | `["en_GB.UTF-8 UTF-8", "en_GB ISO-8859-1", "en_US.UTF-8 UTF-8", "en_US ISO-8859-1" | The supported locales to be installed on the system.                                                                                   |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Bootstrap an Arch Linux system under chroot
  hosts: all
  remote_user: root
  vars:
    bootstrap_mount_dir: /mnt
  roles:
    - role: bootstrap
```
