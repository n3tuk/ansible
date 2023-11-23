# n3t.uk Issue Ansible Role

An Ansible role for the deployment of the issue file for the console, providing
high-level information about the host, either on a monitor or over the display
server through libvirtd, and a warning message about access.

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

| Variable               | Default   | Description                                                                                                                            |
| :--------------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base` | `""`      | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |
| `env_purpose`          | `unknown` | The purpose of the configured server or system, to be displayed on the terminal prompt.                                                |
| `env_name`             | `unknown` | The name of the environment this sytem or server belongs, to be displayed on the terminal prompt.                                      |
| `env_location`         | `unknown` | The location of the configured server or system, to be displayed on the terminal prompt.                                               |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure the issue file
  hosts: all
  remote_user: root
  roles:
    - role: issue
```
