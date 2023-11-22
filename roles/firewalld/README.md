# n3t.uk firewalld Ansible Role

This role configures the firewalld service which provides firewall facilities
for inbound traffic into or through this system.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                      | Default                                | Description                                                                            |
| :---------------------------- | :------------------------------------- | :------------------------------------------------------------------------------------- |
| `firewalld_public_intereface` | `{{ systemd_networkd_ethernet_name }}` | The name of the interface which is configured with the IP addresses for remote access. |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure firewalld with basic zones and services
  hosts: all
  remote_user: root
  roles:
    - role: firewalld
```
