# n3t.uk Proxmox Caddy Ansible Role

An Ansible role for the configuration of the [Caddy][caddy] web server on a
server with the Proxmox service to provide load-balanced services via local and
Tailscale networks.

[caddy]: https://caddyserver.com/

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                          | Default | Description                                                                                                    |
| :-------------------------------- | :------ | :------------------------------------------------------------------------------------------------------------- |
| `env_name`                        | `""`    | (**required**) The environment name to use for the Caddy configuration (e.g., `production`, `development`).    |
| `caddy_acme_cloudflare_enabled`   | `true`  | (**optional**) Whether to enable the Cloudflare DNS challenge for ACME certificate issuance.                   |
| `caddy_acme_cloudflare_api_token` | `""`    | (**optional**) The Cloudflare API token to use for ACME certificate issuance via the Cloudflare DNS challenge. |

## Dependencies

- The `caddy` role is required for installation of the Caddy service first,
  and setting up the default configuration and paths, before this role can
  deploy its configuration.

## Example Playbook

```yaml
---
- name: Add Proxmox Caddy Configuration
  hosts: all
  remote_user: root
  roles:
    - role: caddy
    - role: proxmox/caddy
```
