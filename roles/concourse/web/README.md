# n3t.uk Concourse Web Ansible Role

An Ansible role to install and configure a [Concourse CI][concourse] web node
on Arch Linux. The role configures the Concourse web service, its PostgreSQL
connection, TSA endpoint, native TLS, OIDC, Vault integration, and Caddy
reverse proxy integration.

[concourse]: https://concourse-ci.org/

## Requirements

- Ansible 2.20 or newer.
- An Arch Linux host. The role fails during its pre-flight checks on other
  operating systems.
- The role must not be run while the host is being bootstrapped.
- The supporting CA, Tailscale, Lego, PostgreSQL, Caddy, and UFW configuration
  must be available when the role is used as part of the Concourse play.
- Worker hosts must be present in the `worker` inventory group so that their
  addresses can be used for TSA firewall access.

## Role Variables

### Release and service settings

| Variable                     | Default                 | Description                                                 |
| :--------------------------- | :---------------------- | :---------------------------------------------------------- |
| `concourse_web_version`      | `8.3.0`                 | (**optional**) Concourse release to install.                |
| `concourse_web_cluster_name` | `Concourse`             | (**optional**) Name used to identify the Concourse cluster. |
| `concourse_web_hostname`     | `concourse.example.com` | (**optional**) Public hostname used by the web service.     |

The role downloads the Linux amd64 release from GitHub and verifies it with
the checksum defined in `vars/main.yaml`. The binary is installed below
`/usr/lib/concourse`, application data is stored below `/var/lib/concourse`,
and the service is named `concourse-web`.

### TLS and Caddy

| Variable                                            | Default                                          | Description                                                  |
| :-------------------------------------------------- | :----------------------------------------------- | :----------------------------------------------------------- |
| `concourse_web_tls_bind_port`                       | `8443`                                           | (**optional**) Native Concourse TLS port.                    |
| `concourse_web_tls_cert`                            | `{{ concourse_web_lib_path }}/certs/server.crt`  | (**optional**) Concourse TLS certificate path.               |
| `concourse_web_tls_key`                             | `{{ concourse_web_lib_path }}/certs/server.key`  | (**optional**) Concourse TLS private key path.               |
| `concourse_web_tls_ca_cert`                         | `/etc/ssl/certs.pem`                             | (**optional**) CA certificate bundle path.                   |
| `concourse_web_caddy_tls_acme_server`               | `https://acme-v02.api.letsencrypt.org/directory` | (**optional**) ACME directory used by Caddy.                 |
| `concourse_web_caddy_tls_acme_email`                | `concourse@{{ concourse_web_hostname }}`         | (**optional**) Email address used for ACME registration.     |
| `concourse_web_caddy_tls_dns_rfc2136_server`        | `dns.example.com`                                | (**optional**) RFC 2136 DNS server used for ACME challenges. |
| `concourse_web_caddy_tls_dns_rfc2136_key_name`      | `example-key`                                    | (**optional**) RFC 2136 TSIG key name.                       |
| `concourse_web_caddy_tls_dns_rfc2136_key_algorithm` | `hmac-sha256`                                    | (**optional**) RFC 2136 TSIG key algorithm.                  |
| `concourse_web_caddy_tls_dns_rfc2136_key_secret`    | `this-should-be-secret`                          | (**required**) RFC 2136 TSIG key secret; store it securely.  |

Caddy obtains the public certificate and proxies HTTPS traffic to the native
Concourse TLS listener. Replace all example DNS and secret values before using
the role.

### PostgreSQL

| Variable                            | Default              | Description                                            |
| :---------------------------------- | :------------------- | :----------------------------------------------------- |
| `concourse_web_postgresql_host`     | `localhost`          | (**required**) PostgreSQL hostname or address.         |
| `concourse_web_postgresql_port`     | `5432`               | (**optional**) PostgreSQL port.                        |
| `concourse_web_postgresql_sslmode`  | `verify-full`        | (**optional**) PostgreSQL SSL mode.                    |
| `concourse_web_postgresql_ca_cert`  | `/etc/ssl/certs.pem` | (**optional**) PostgreSQL CA certificate path.         |
| `concourse_web_postgresql_username` | `concourse`          | (**optional**) PostgreSQL username.                    |
| `concourse_web_postgresql_password` | unset                | (**required**) PostgreSQL password; store it securely. |
| `concourse_web_postgresql_database` | `atc`                | (**optional**) PostgreSQL database name.               |

Set `concourse_web_postgresql_manager` outside the role when the play should
create or manage the PostgreSQL database and user. The Concourse play enables
the PostgreSQL role before this role is applied.

### Encryption and TSA

| Variable                                  | Default | Description                                                       |
| :---------------------------------------- | :------ | :---------------------------------------------------------------- |
| `concourse_web_encryption_key_base64`     | unset   | (**required**) Base64-encoded key used to encrypt Concourse data. |
| `concourse_web_old_encryption_key_base64` | unset   | (**optional**) Previous key used during encryption-key rotation.  |
| `concourse_web_tsa_bind_ip`               | unset   | (**optional**) Address on which the TSA listener binds.           |
| `concourse_web_tsa_bind_port`             | `2222`  | (**optional**) TSA listener port.                                 |

The role creates TSA host, authorized-worker, and session-signing keys under
the Concourse data directory. The TSA port is opened only for worker hosts
known through the inventory and their Tailscale addresses.

### OIDC

| Variable                             | Default                    | Description                                        |
| :----------------------------------- | :------------------------- | :------------------------------------------------- |
| `concourse_web_oidc_display_name`    | empty                      | (**optional**) Display name for the OIDC provider. |
| `concourse_web_oidc_issuer`          | `https://oidc.example.com` | (**required**) OIDC issuer URL.                    |
| `concourse_web_oidc_ca_cert`         | `/etc/ssl/certs.pem`       | (**optional**) OIDC CA certificate path.           |
| `concourse_web_oidc_client_id`       | empty                      | (**required**) OIDC client ID.                     |
| `concourse_web_oidc_client_secret`   | empty                      | (**required**) OIDC client secret.                 |
| `concourse_web_oidc_scope`           | `openid profile email`     | (**optional**) OIDC scopes to request.             |
| `concourse_web_oidc_username_key`    | `email`                    | (**optional**) OIDC username claim.                |
| `concourse_web_oidc_groups_key`      | `groups`                   | (**optional**) OIDC groups claim.                  |
| `concourse_web_oidc_main_team_user`  | empty                      | (**optional**) OIDC user for the main team.        |
| `concourse_web_oidc_main_team_group` | empty                      | (**optional**) OIDC group for the main team.       |

### Vault

| Variable                                    | Default                          | Description                                      |
| :------------------------------------------ | :------------------------------- | :----------------------------------------------- |
| `concourse_web_vault_url`                   | `https://vault.example.com:8200` | (**required**) Vault URL.                        |
| `concourse_web_vault_auth_backend`          | `approle`                        | (**optional**) Vault authentication backend.     |
| `concourse_web_vault_auth_role_id`          | unset                            | (**required**) Vault AppRole role ID.            |
| `concourse_web_vault_auth_secret_id`        | unset                            | (**required**) Vault AppRole secret ID.          |
| `concourse_web_vault_auth_backend_max_ttl`  | `1h`                             | (**optional**) Maximum Vault authentication TTL. |
| `concourse_web_vault_path_prefix`           | `concourse`                      | (**optional**) Vault path prefix.                |
| `concourse_web_vault_enable_kv_mount_cache` | `true`                           | (**optional**) Enable Vault KV mount caching.    |
| `concourse_web_vault_lookup_templates`      | `[]`                             | (**optional**) Vault lookup templates.           |

All passwords, keys, client secrets, AppRole IDs, and TSIG secrets should be
provided through encrypted variables rather than committed in plaintext.

## Dependencies

This role has no Galaxy role dependencies declared in `meta/main.yaml`. The
repository’s `plays/concourse.yaml` play composes it with the following roles:

- `ca` and `lego` provide certificate and renewal support.
- `tailscale` provides the private network used by the web and worker nodes.
- `cloudflared` provides the configured Cloudflare integration.
- `postgresql` provides the Concourse database.
- `caddy` provides the public HTTPS reverse proxy.
- `ufw` must be available for the TSA firewall rules.

The play gathers facts from the CA and worker inventory groups before applying
this role. Use the `concourse-web` play tag to run the web role entry point.

## Example Playbook

```yaml
---
- name: Deploy the Concourse web service
  hosts: web
  become: true
  become_user: root
  roles:
    - role: ca
    - role: tailscale
    - role: cloudflared
    - role: lego
    - role: postgresql
    - role: caddy
    - role: concourse/web
      tags:
        - concourse-web
```

The complete repository play is available at
[`plays/concourse.yaml`][concourse-play]. It also configures the worker nodes
and supplies the supporting role integrations described above.

[concourse-play]: https://github.com/n3tuk/ansible/blob/main/plays/concourse.yaml
