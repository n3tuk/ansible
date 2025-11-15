# n3t.uk PostgreSQL Ansible Role

An Ansible role for the installation and basic configuration of the PostgreSQL
database service, including both standalone and primary/secondary replication
modes.

## Setting up Replication

This role will establish replication between a primary server and one or more
secondary servers, based on the setting of the `postgresql_replication_role`
variable for each server.

However, this assumes that the replication is being set up on a server
from-scratch. An existing server being converted in to a standby server will
likely fail is the `postgresql_data_path` (i.e. `/var/lib/postgresql/data`) is
not empty. Converting a standalone to a primary server should work correctly as
this is just an update to the configuration file and the creation of a `ROLE`
and slots for replication.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                          | Default      | Description                                                                                                    |
| :-------------------------------- | :----------- | :------------------------------------------------------------------------------------------------------------- |
| `postgresql_listen_addresses`     | `['*']`      | (**optional**) The list of IP adresses to which PostgreSQL will bind on startup.                               |
| `postgresql_port`                 | `5432`       | (**optional**) The port number to which PostgreSQL will bind on startup.                                       |
| `postgresql_max_connections`      | `100`        | (**optional**) The maximum number of connections allowed to this server.                                       |
| `postgresql_allowed_hosts`        | `[]`         | (**optional**) The list of hosts (using their inventory name) which will be allowed to connect to this server. |
| `postgresql_replication_role`     | `standalone` | (**optional**) The mode of operation for PostgreSQL (`standalone`, `primary`, or `secondary`).                 |
| `postgresql_replication_hosts`    | `[]`         | (**optional**) The list of hosts (using their inventory name) which will replicate from the primary server.    |
| `postgresql_replication_primary`  | `""`         | (**optional**) The inventory hostname of the PostgreSQL which will be the primary host for replication.        |
| `postgresql_replication_username` | `replicator` | (**optional**) The username that standby servers will use to connect to the primary server for replication.    |
| `postgresql_replication_password` | `replicator` | (**optional**) The password that standby servers will use to connect to the primary server for replication.    |

## Dependencies

The `ufw` role is recommended to manage the firewall rules for PostgreSQL access.

## Example Playbook

```yaml
---
- name: Install PostgreSQL
  hosts: all
  remote_user: root
  vars:
    postgresql_replication_role: standalone
  roles:
    - role: ufw
    - role: postgresql
```
