# n3t.uk Bird Ansible Role

An Ansible role for the installation and configuration of Bird to help manage
internal routing services, such as providing iBGP to enable highly-available
routing for load balancing services.

## Requirements

None.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure the bird service
  hosts: all
  become: true
  become_user: root
  roles:
    - role: bird
```
