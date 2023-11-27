# n3t.uk logrotate Ansible Role

An Ansible role for the installation and configuration of logrotate, setting the
default configuration and preparing the timer for the service.

## Requirements

None other than the Ansible Role.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure logrotate
  hosts: all
  become: true
  become_user: root
  roles:
    - role: logrotate
``
```
