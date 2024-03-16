# n3t.uk Kubernetes (k3s) Ansible Role

An Ansible role for the host preparation for the deployment of a Kubernetes
cluster, in both single-node and multi-node configurations, alongside the
creation and mounting of filesystems and the installation and configuration of
required system packages.

## Requirements

None.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure a Kubernetes node
  hosts: all
  become: true
  become_user: root
  roles:
    - role: k3s
```
