# n3t.uk OpenBao Ansible Role

An Ansible role for the installation and configuration of the [OpenBao][openbao]
secrets manager service.

[openbao]: https://openbao.org/

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                                      | Default                    | Description                                                                                                |
| :-------------------------------------------- | :------------------------- | :--------------------------------------------------------------------------------------------------------- |
| `openbao_domain_name`                         | `secrets.services.n3t.uk`  | (**optional**) The domain name for the OpenBao service endpoint.                                           |
| `openbao_service_port`                        | `8200`                     | (**optional**) The port for the OpenBao service to listen on.                                              |
| `openbao_cluster_port`                        | `8201`                     | (**optional**) The port for the OpenBao cluster to listen on.                                              |
| `openbao_metrics_port`                        | `9201`                     | (**optional**) The port for the OpenBao metrics to listen on.                                              |
| `openbao_caddy_tls_acme_server`               | `"{{ lego_acme_server }}"` | (**optional**) The ACME server URL for Caddy TLS certificate issuance.                                     |
| `openbao_caddy_tls_acme_email`                | `"{{ lego_acme_email }}"`  | (**optional**) The email address for Caddy ACME certificate issuance.                                      |
| `openbao_caddy_tls_dns_rfc2136_server`        | `""`                       | (**optional**) The DNS server address for RFC 2136 dynamic DNS updates used by Caddy.                      |
| `openbao_caddy_tls_dns_rfc2136_key_name`      | `""`                       | (**optional**) The TSIG key name for RFC 2136 dynamic DNS updates used by Caddy.                           |
| `openbao_caddy_tls_dns_rfc2136_key_algorithm` | `""`                       | (**optional**) The TSIG key algorithm for RFC 2136 dynamic DNS updates used by Caddy.                      |
| `openbao_caddy_tls_dns_rfc2136_key_secret`    | `""`                       | (**optional**) The TSIG key secret for RFC 2136 dynamic DNS updates used by Caddy.                         |
| `openbao_caddy_health_interval`               | `10s`                      | (**optional**) The interval between health checks for the Caddy upstream.                                  |
| `openbao_caddy_health_timeout`                | `2s`                       | (**optional**) The timeout for health checks for the Caddy upstream.                                       |
| `openbao_caddy_health_fails`                  | `2`                        | (**optional**) The number of consecutive health check failures before marking an upstream as unhealthy.    |
| `openbao_caddy_retry_duration`                | `5s`                       | (**optional**) The duration for Caddy to wait before retrying a failed upstream request.                   |
| `openbao_caddy_retry_interval`                | `250ms`                    | (**optional**) The interval between Caddy retry attempts for a failed upstream request.                    |
| `openbao_caddy_passive_fail_duration`         | `30s`                      | (**optional**) The duration for Caddy passive health checks to consider an upstream as failed.             |
| `openbao_caddy_passive_max_fails`             | `1`                        | (**optional**) The maximum number of failures for Caddy passive health checks before marking unhealthy.    |
| `openbao_group_name`                          | `openbao`                  | (**optional**) The group name for the OpenBao nodes in the inventory.                                      |
| `openbao_raft_non_voter`                      | `false`                    | (**optional**) Whether the OpenBao node should be a non-voting member of the Raft cluster.                 |
| `openbao_raft_min_quorum`                     | `2`                        | (**optional**) The minimum number of voting members required for the OpenBao Raft cluster to reach quorum. |
| `openbao_raft_backup_cluster_name`            | `""`                       | (**optional**) The cluster name to use in the S3 backup path for raft snapshots.                           |
| `openbao_raft_backup_approle_path`            | `""`                       | (**optional**) The AppRole auth mount path used for authenticating the raft backup process.                |
| `openbao_raft_backup_approle_role_id`         | `""`                       | (**optional**) The AppRole Role ID used for authenticating the raft backup process.                        |
| `openbao_raft_backup_approle_secret_id`       | `""`                       | (**optional**) The AppRole Secret ID used for authenticating the raft backup process.                      |
| `openbao_raft_backup_bucket_name`             | `""`                       | (**optional**) The AWS S3 bucket name to upload raft snapshots to.                                         |
| `openbao_raft_backup_bucket_prefix`           | `openbao`                  | (**optional**) The key prefix within the S3 bucket for raft snapshot uploads.                              |

## Dependencies

- The `ufw` role is required to manage the firewall rules for the OpenBao
  service.
- The `tailscale` role is required to allow OpenBao to provide its services to,
  and communicate over, the Tailnet network.
- The `ca` and `lego` roles are required to manage the mTLS certificates for
  the OpenBao service.
- The `caddy` role is required to provide a reverse proxy for distributing
  requests across the OpenBao cluster via Tailscale Service addresses.

## Example Playbook

```yaml
---
- name: Deploy the OpenBao Service
  hosts: openbao
  become: true
  become_user: root
  roles:
    - role: ca
    - role: lego
    - role: caddy
    - role: openbao
```
