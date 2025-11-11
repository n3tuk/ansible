# n3t.uk Caddy Ansible Role

An Ansible role for the installation and configuration of the [Caddy][caddy]
web server on a server with a default configuration.

[caddy]: https://caddyserver.com/

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                          | Default                                                                  | Description                                                                                                                                                                   |
| :-------------------------------- | :----------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `caddy_user`                      | `caddy`                                                                  | (**optional**) The user to create for running the Caddy service.                                                                                                              |
| `caddy_group`                     | `caddy`                                                                  | (**optional**) The group to create for running the Caddy service.                                                                                                             |
| `caddy_url_base`                  | `https://caddyserver.com/api/download`                                   | (**optional**) The base URL to download Caddy from (assuming compatibility with Caddy's API).                                                                                 |
| `caddy_packages`                  | `["github.com/caddy-dns/cloudflare"]`                                    | (**optional**) A list of Caddy packages to include in the build when downloading from Caddy's API.                                                                            |
| `caddy_upgrade`                   | `false`                                                                  | (**optional**) Whether to upgrade Caddy to the latest version, or add new features, if already installed (in order to keep versions consistent, this is disabled by default). |
| `caddy_acme_email`                | `admin@n3t.uk`                                                           | (**optional**) The email address to use for ACME certificate issuance.                                                                                                        |
| `caddy_acme_cloudflare_enabled`   | `true`                                                                   | (**optional**) Whether to enable the Cloudflare DNS challenge for ACME certificate issuance.                                                                                  |
| `caddy_acme_cloudflare_api_token` | `""`                                                                     | (**optional**) The Cloudflare API token to use for ACME certificate issuance via the Cloudflare DNS challenge.                                                                |
| `caddy_tls_resolvers`             | `["1.1.1.1", "1.0.0.1", "2606:4700:4700::1111", "2606:4700:4700::1001"]` | (**optional**) A list of DNS resolvers to configure Caddy to use for resolution during DNS challenges.                                                                        |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install Caddy
  hosts: all
  remote_user: root
  roles:
    - role: caddy
```
