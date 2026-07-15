# n3t.uk OpenBao Ansible Role

An Ansible role for the installation and configuration of the [OpenBao][openbao]
secrets manager service.

[openbao]: https://openbao.org/

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable | Default | Description |
| :------- | :------ | :---------- |
| `openbao_service_port` | `8200` | (**optional**) The port for the OpenBao service to listen on. |
| `openbao_cluster_port` | `8201` | (**optional**) The port for the OpenBao cluster to listen on. |
| `openbao_metrics_port` | `9201` | (**optional**) The port for the OpenBao metrics to listen on. |
| `openbao_group_name` | `openbao` | (**optional**) The group name for the OpenBao nodes in the inventory. |
| `openbao_raft_non_voter` | `false` | (**optional**) Whether the OpenBao node should be a non-voting member of the Raft cluster. |
| `openbao_raft_min_quorum` | `2` | (**optional**) The minimum number of voting members required for the OpenBao Raft cluster to reach quorum. |

## Dependencies

- The `ufw` role is required to manage the firewall rules for the OpenBao
  service.
- The `tailscale` role is required to allow OpenBao to provide its services to,
  and communicate over, the Tailnet network
- The `ca` and `lego`  roles are required to manage the mTLS certificates for
  the OpenBao service.

## Example Playbook

```yaml
---
- name: Install OpenBao
  hosts: all
  remote_user: root
  roles:
    - role: ca
    - role: ufw
    - role: tailscale
    - role: lego
    - role: openbao
```
