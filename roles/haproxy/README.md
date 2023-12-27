# n3t.uk HAProxy Ansible Role

An Ansible role for the configuration of HAProxy to provide internal
load-balancing functions outside of the Kubernetes clusters.

## Requirements

None.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure the HAProxy service
  hosts: all
  become: true
  become_user: root
  roles:
    - role: haproxy
```
