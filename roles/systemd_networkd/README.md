# n3t.uk systemd-networkd Ansible Role

This role configures the networking for the system using `systemd-networkd`,
with support for a singe ethernet interface, and then VLANs and Bridges attached
to it.

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

| Variable                         | Default   | Description                                                                                                                            |
| :------------------------------- | :-------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base`           | `""`      | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |
| `systemd_networkd_ethernet_name` | `enp86s0` | The name of the ethernet interface which will be matched with the ethernet configuraiton deployed.                                     |
| `systemd_networkd_ethernet_mtu`  | `1500`    | The MTU value to be applied to the ethernet interface and VLAN intetrfaces deployed.                                                   |
| `systemd_networkd_vlans`         | `[32]`    | The list of VLANs which should be configured on the system using 802.1q tagged packets.                                                |
| `systemd_networkd_access_vlan`   | `32`      | The ID of the VLAN which should be configured with IPv4 and/or IPv6 addresses, allowing access to this sytem on the network.           |
| `systemd_networkd_ipv4_address`  | `""`      | The IPv4 address/prefix which should be configured to the access VLAN on this system.                                                  |
| `systemd_networkd_ipv4_gateway`  | `""`      | The IPv4 gateway which should be configured to the access VLAN on this system.                                                         |
| `systemd_networkd_ipv6_address`  | `""`      | The IPv6 address/prefix which should be configured to the access VLAN on this system.                                                  |

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
