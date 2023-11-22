# n3t.uk Starship Ansible Role

An Ansible role for the installation and configuration of the terminal utility
[Starship][starship-rs] for the `root` user and new users (using the `/etc/skel`
directory).

[starship-rs]: https://starship.rs/

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

| Variable               | Default               | Description                                                                                                                            |
| :--------------------- | :-------------------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base` | `""`                  | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |
| `starship_config_dirs` | `["root","etc/skel"]` | The list of directories where `.config/starship.toml` is to be installed.                                                              |
| `env_purpose`          | `unknown`             | The purpose of the configured server or system, to be displayed on the terminal prompt.                                                |
| `env_name`             | `unknown`             | The name of the environment this sytem or server belongs, to be displayed on the terminal prompt.                                      |
| `env_location`         | `unknown`             | The location of the configured server or system, to be displayed on the terminal prompt.                                               |
| `env_colour`           | `d8dee9`              | The colour to be given to the `env_name` output on the terminal prompt.                                                                |
>>>>>>> 97d8928 (Add a role to manage the issue file for console access)

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure Starship
  hosts: all
  remote_user: root
  roles:
    - role: starship
```
