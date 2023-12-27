# n3t.uk Vault Ansible Role

An Ansible role for the configuration of Hashicorp Vault host which will
facilitate the storage and management of secrets, as well as provide an endpoint
for a local Certificate Authority for the management of private certificates in
each environment.

## Requirements

It is expected that the `haproxy` and `bird` role be configured prior to this
role so that the load balancing services are configured on the host prior to the
start of the service.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure the Hashicorp Vault service
  hosts: all
  become: true
  become_user: root
  roles:
    - role: bird
    - role: haproxy
    - role: vault
```
