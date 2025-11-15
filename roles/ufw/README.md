# n3t.uk UFW Ansible Role

An Ansible role for the installation and basic configuration of the UFW firewall
service, which, by default, simply allows SSH access from a set of private IP
addresses.

## Requirements

None other than the Ansible Role.

## Default Rules

The following table represents the default UFW rules that will be applied by
this role to all systems it is run against. Additional rules can be added via the
`ufw_extra_rules` variable.

| `rule` | `from_ip`                       | `proto` | `port` | `comment`               |
| :----- | :------------------------------ | :-----: | :----: | :---------------------- |
| allow  | `172.27.4.128/26`               |   tcp   |   22   | Allow SSH from @private |
| allow  | `2a02:8010:8006:7a22::/64`      |   tcp   |   22   | Allow SSH from @private |
| allow  | `2a00:1098:0:82:1000:a:ea:b72c` |   tcp   |   22   | Allow SSH from Owain    |

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
  vars:
    ufw_extra_rules:
      - rule: allow
        from_ip: 10.0.0.0/8
        proto: tcp
        port: 80
        comment: Allow HTTP from internal network
  roles:
    - role: ufw
```
