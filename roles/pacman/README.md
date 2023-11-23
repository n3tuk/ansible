# n3t.uk pacman Ansible Role

Install and configure the pacman application for access to Arch Linux
repositories and update configuration.

## Requirements

None, except for this role.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure pacman
  hosts: all
  become_user: root
  roles:
    - role: pacman
```
