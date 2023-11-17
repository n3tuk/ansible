# n3t.uk systemd Ansible Role

This role configures the common services run under `systemd`, including logging
(`jouranld`), the OOM manager (`oomd`), and NTP management (`timesyncd`).

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

| Variable                          | Default                                                                    | Description                                                                                                                                     |
| :-------------------------------- | :------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base`            | `""`                                                                       | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs.          |
| `systemd_system_watchdog_enabled` | `false`                                                                    | Set whether or not to enabled the Watchdog via `systemd`, allowing the system to be automatically rebooted in the event of the system freezing. |
| `systemd_journald_max_use`        | `256M`                                                                     | Set the maximum size for `journald` when saving log files to persistent storage.                                                                |
| `systemd_timesyncd_fallback_ntp`  | `["0.pool.ntp.org", "1.pool.ntp.org", "2.pool.ntp.org", "3.pool.ntp.org"]` | Set the NTP servers to be used by the system in the event that it cannot find any to use locally.                                               |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure common systemd services
  hosts: all
  remote_user: root
  roles:
    - role: systemd
```
