# n3t.uk Authentik Ansible Role

An Ansible role for the installation and configuration of the Authentik
authentication service, connecting with PostgreSQL and Redis backends.

## Requirements

None other than the Ansible Role.

## Role Variables

None.

## Dependencies

- The `ufw` role is required to manage the firewall rules for Authentik access.
- The `redis` and `postgresql` roles are required to provide the necessary
  backend services for Authentik to connect and manage data and caching.
- The `caddy` role is required to provide the web server and reverse proxy for
  Authentik, including providing Let's Encrypt TLS certificates for secure HTTPS
  access.
- The `cloudflared` role is recommended to provide secure tunnelling to
  Authentik from external networks.
- The `tailscale` role is recommended to provide secure tunneling to Authentik
  from private networks.

## Example Playbook

```yaml
---
- name: Install Authentik
  hosts: all
  remote_user: root
  roles:
    - role: ufw
    - role: postgresql
    - role: redis
    - role: caddy
    - role: cloudflared
    - role: tailscale
    - role: authentik
```
