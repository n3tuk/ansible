# n3t.uk NetData Ansible Role

An Ansible role for the installation and configuration of Netdata, supporting
either Parent nodes to collate and store data centrally, or Child nodes to
collect the data and stream it to the parent nodes.

## Requirements

None other than the Ansible Role.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure netdata
  hosts: all
  become: true
  become_user: root
  roles:
    - role: netdata
```
