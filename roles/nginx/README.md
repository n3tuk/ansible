# n3t.uk nginx Ansible Role

An Ansible role for the installation and basic configuration of nginx, setting
up the `http{}` service with standard settings and optimisations, alongside a
simple `localhost` `server{}` block to support local monitoring of the service.

## Requirements

None other than the Ansible Role.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure the nginx service
  hosts: all
  become: true
  become_user: root
  roles:
    - role: nginx
```
