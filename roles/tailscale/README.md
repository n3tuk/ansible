# n3t.uk Tailscale Ansible Role

An Ansible role for the installation and configuration of the
[Tailscale][tailscale] VPN client and connect it to a Tailscale network.

[tailscale]: https://tailscale.com/

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                    | Default | Description                                                                                                                                  |
| :-------------------------- | :------ | :------------------------------------------------------------------------------------------------------------------------------------------- |
| `tailscale_auth_key`        | `""`    | (**required**) The Authorisation Key created in the Tailscale Admin Console to allow this client to join the Tailscale network.              |
| `tailscale_args`            | `""`    | (**optional**) Additional command-line arguments to add to the Tailscale CLI when joining the Tailscale network.                             |
| `tailscale_up_timeout`      | `120`   | (**optional**) The number of seconds to wait for Tailscale to connect to the network after running `tailscale up`.                           |
| `tailscale_port`            | `41641` | (**optional**) The port to use for Tailscale VPN client access on this server.                                                               |
| `tailscale_exit_node`       | `false` | (**optional**) Whether to configure this Tailscale client as an exit node for the Tailscale network.                                         |
| `tailscale_accept_routes`   | `true`  | (**optional**) Whether to accept routes advertised by other Tailscale nodes on the network.                                                  |
| `tailscale_hostname`        | `""`    | (**optional**) If set, override the hostname that this Tailscale client uses on the Tailscale network from the one configured on the system. |
| `tailscale_ssh`             | `true`  | (**optional**) Whether to enable Tailscale SSH on this device.                                                                               |
| `tailscale_peer_relay`      | `false` | (**optional**) Whether to enable Tailscale Peer Relay on this server.                                                                        |
| `tailscale_peer_relay_port` | `41642` | (**optional**) The port to use for Tailscale Peer Relay access on this server.                                                               |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install Tailscale
  hosts: all
  remote_user: root
  roles:
    - role: tailscale
```
