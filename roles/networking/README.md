# n3t.uk Proxmox Networking Ansible Role

An Ansible role for the configuration of the networking on [Proxmox][proxmox]
servers, specifically those using Thunderbolt networking to form a mesh
high-speed network between nodes.

[proxmox]: https://www.proxmox.com/en/

## Requirements

This role can only run on Debian-based systems, specifically Proxmox VE hosts.

## Role Variables

| Variable                          | Default                | Description                                                                                                          |
| :-------------------------------- | :--------------------- | :------------------------------------------------------------------------------------------------------------------- |
| `networking_loopback_address`     | `fc00::1`              | The IPv6 loopback address to assign to the `lo` interface to be used for Proxmox and Ceph inter-node communications. |
| `networking_openfabric_router_id` | `49.0000.0000.0001.00` | The OpenFabric Router ID to assign to this Proxmox Node for sharing routes between nodes.                            |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure Proxmox Networking
  hosts: all
  remote_user: root
  roles:
    - role: networking
```
