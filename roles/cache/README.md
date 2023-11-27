# n3t.uk nginx Caching Ansible Role

An Ansible role for the configuration of an nginx virtual host which will
facilitate the local caching of Arch Linux repositories within the network, for
both the generate repositories provided by Arch Linux, as well as the private
n3t.uk repository.

## Requirements

It is expected that the `nginx` role be configured prior to this role so that
the correct directories are created and the service can be reloaded on
configuration changes.

## Role Variables

None.

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Configure the nginx cache for Arch Linux
  hosts: all
  become: true
  become_user: root
  roles:
    - role: nginx
    - role: cache
```
