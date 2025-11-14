# n3t.uk Valkey Ansible Role

An Ansible role for the installation and basic configuration of the Valkey
in-memory data store service, including both stand-alone and cluster replication
modes.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                      | Default                    | Description                                                                                                                                                           |
| :---------------------------- | :------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `valkey_bind`                 | `["0.0.0.0","::"]`         | (**optional**) The IP addresses Valkey will bind to on startup.                                                                                                       |
| `valkey_port`                 | `6379`                     | (**optional**) The port Valkey will listen on startup (also sets the cluster port at +10000 if clustering is enabled).                                                |
| `valkey_allowed_hosts`        | `[]`                       | (**optional**) A list of ansible hosts (using their inventory hostname) allowed to connect to Valkey.                                                                 |
| `valkey_cluster_enabled`      | `false`                    | (**optional**) Whether to enable Valkey clustering mode, including setting up the firewall and ACL rules.                                                             |
| `valkey_replication_primary`  | `"localhost"`              | (**optional**) The hostname or IP address of the primary Valkey node for replication.                                                                                 |
| `valkey_replication_username` | `"replicator"`             | (**optional**) The username for Valkey replication authentication.                                                                                                    |
| `valkey_replication_password` | `"replicator"`             | (**optional**) The password for Valkey replication authentication.                                                                                                    |
| `valkey_loglevel`             | `"notice"`                 | (**optional**) The logging level for Valkey.                                                                                                                          |
| `valkey_max_clients`          | `1000`                     | (**optional**) The maximum number of client connections Valkey will accept.                                                                                           |
| `valkey_tls_enabled`          | `false`                    | (**optional**) Whether to enable TLS encryption for Valkey connections.                                                                                               |
| `valkey_tls_ca_cert_file`     | `"/etc/valkey/ca.crt"`     | (**optional**) The path to the CA certificate file for TLS validation.                                                                                                |
| `valkey_tls_cert_file`        | `"/etc/valkey/valkey.crt"` | (**optional**) The path to the Valkey server certificate file for TLS.                                                                                                |
| `valkey_tls_key_file`         | `"/etc/valkey/valkey.key"` | (**optional**) The path to the Valkey server private key file for TLS.                                                                                                |
| `valkey_tls_client_cert_file` | `"/etc/valkey/client.crt"` | (**optional**) The path to the Valkey client certificate file for TLS (if the server certificate cannot be used for client authentication).                           |
| `valkey_tls_client_key_file`  | `"/etc/valkey/client.key"` | (**optional**) The path to the Valkey client private key file for TLS (if the server certificate cannot be used for client authentication).                           |
| `valkey_acl_users`            | `[]`                       | (**optional**) A list of ACL user definitions for Valkey. Each user should be a dictionary with keys: `name`, `enabled`, `password`, `rules`, and optional `comment`. |

## Dependencies

- The `ufw` role is recommended to manage the firewall rules for Valkey access.

## Example Playbook

```yaml
---
- name: Install Valkey
  hosts: all
  remote_user: root
  roles:
    - role: ufw
    - role: valkey
```
