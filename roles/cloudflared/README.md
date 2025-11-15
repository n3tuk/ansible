# n3t.uk cloudflared Ansible Role

An Ansible role for the installation and configuration of the
[cloudflared][cloudflared] client and to connect it to a Cloudflare network
ready to serve inbound traffic.

[cloudflared]: https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                    | Default  | Description                                                                                                                |
| :-------------------------- | :------- | :------------------------------------------------------------------------------------------------------------------------- |
| `cloudflared_account_tag`   | `""`     | (**required**) The Cloudflare Account ID to use to authenticate this client with.                                          |
| `cloudflared_tunnel_name`   | `tunnel` | (**optional**) The name of the `cloudflared` Tunnel to connect to (this should match the name given in the control panel). |
| `cloudflared_tunnel_id`     | `""`     | (**required**) The ID of the `cloudflared` Tunnel to connect to (this should be the UUID provided by Cloudflare).          |
| `cloudflared_tunnel_secret` | `""`     | (**required**) The token for the `cloudflared` Tunnel to connect to Cloudflare with.                                       |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install cloudflared
  hosts: all
  remote_user: root
  roles:
    - role: cloudflared
```
