# n3t.uk Concourse Worker Ansible Role

An Ansible role to install and configure a [Concourse CI][concourse] worker on
Arch Linux. The role installs the worker binary, configures its TSA connection,
and manages the `concourse-worker` systemd service.

[concourse]: https://concourse-ci.org/

## Requirements

- Ansible 2.20 or newer.
- An Arch Linux host. The role fails during its pre-flight checks on other
  operating systems.
- The role must not be run while the host is being bootstrapped.
- Tailscale must be configured before this role so the worker can reach the web
  node’s TSA endpoint.
- A Concourse web node must be configured with the worker’s private key and
  authorized to accept the worker’s connection.

## Role Variables

| Variable                            | Default                    | Description                                           |
| :---------------------------------- | :------------------------- | :---------------------------------------------------- |
| `concourse_worker_version`          | `8.3.0`                    | (**optional**) Concourse release to install.          |
| `concourse_worker_name`             | `{{ inventory_hostname }}` | (**optional**) Name reported by the worker.           |
| `concourse_worker_key`              | unset                      | (**required**) Private worker key; store it securely. |
| `concourse_worker_tsa_host`         | unset                      | (**required**) Web node TSA hostname or address.      |
| `concourse_worker_tsa_port`         | `2222`                     | (**optional**) Web node TSA port.                     |
| `concourse_worker_tsa_host_key_pub` | unset                      | (**required**) Public TSA host key from the web node. |

The repository playbook normally sets `concourse_worker_tsa_host` to the web
node’s Tailscale address and provides the matching public TSA host key. Each
worker must have a unique `concourse_worker_key`.

The role downloads the Linux amd64 release from GitHub and verifies it with
the checksum defined in `vars/main.yaml`. The binary is installed below
`/usr/lib/concourse`, worker data is stored below `/var/lib/concourse`, and the
service is named `concourse-worker`.

## Dependencies

This role has no Galaxy role dependencies declared in `meta/main.yaml`. The
repository’s `plays/concourse.yaml` play applies `tailscale` immediately before
this role so the worker can connect to the web node over the private network.
The web role must be configured with the corresponding worker key and TSA
authorization before the worker service can connect.

Use the `concourse-worker` play tag to run the worker role entry point. Worker
configuration writes the TSA public key and private worker key with Ansible
logging disabled, and the service runs as root because Concourse manages
containers and Baggageclaim resources.

## Example Playbook

```yaml
---
- name: Deploy a Concourse worker
  hosts: worker
  become: true
  become_user: root
  roles:
    - role: tailscale
    - role: concourse/worker
      tags:
        - concourse-worker
```

The complete repository play is available at
[`plays/concourse.yaml`][concourse-play]. Set the worker variables through
inventory or encrypted group/host variables before running the play.

[concourse-play]: https://github.com/n3tuk/ansible/blob/main/plays/concourse.yaml
