# n3t.uk UFW Ansible Role

An Ansible role for the installation and basic configuration of the UFW firewall
service.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable          | Default | Description                                                                            |
| :---------------- | :------ | :------------------------------------------------------------------------------------- |
| `ufw_extra_rules` | `[]`    | (**optional**) A list of maps defining extra UFW rules to add to the configured hosts. |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install UFW
  hosts: all
  remote_user: root
  roles:
    - role: ufw
```
