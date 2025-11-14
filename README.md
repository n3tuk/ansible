# n3t.uk Ansible Playbooks

An Ansible repository for the configuration of systems and resources managed by
n3tuk.

## Playbooks

| Playbook                           | `task`&nbsp;Command          | Description                                                                                                                                      |
| :--------------------------------- | :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| `n/a`                              | [`develop`][taskfile]        | A task-specific command which will run linting and validation of the code and configuration within this repository.                              |
| `n/a`                              | [`ping`][taskfile]           | A task-specific command which will attempt to `ping` all hosts configured in the [`inventory`][inventory] using the standard `become` process.   |
| [`bootstrap.yaml`][play-bootstrap] | [`play:bootstrap`][taskfile] | A play which will bootstrap any host listed under the bootstrap group, and is normally used for both physical nodes as well as virtual machines. |
| [`baseline.yaml`][play-baseline]   | [`play:baseline`][taskfile]  | A play which will configure physical and virtual machines to baselined settings.                                                                 |
| [`users.yaml`][play-users]         | [`play:users`][taskfile]     | A play which will create or update of all the users and groups on a system.                                                                      |
| [`update.yaml`][play-update]       | [`update`][taskfile]         | A play which will update all Arch Linux repositories of using `pacman` (but not upgrade the packages).                                           |
| [`upgrade.yaml`][play-upgrade]     | [`upgrade`][taskfile]        | A play which will update and upgrade all Arch Linux packages of using `pacman`.                                                                  |
| [`authentik.yaml`][play-authentik] | [`play:authentik`][taskfile] | A play which will deploy and configure the Authentik identity provider alongside PostgreSQL, Redis, and `cloudflared`.                           |

All Ansible plays run via `task` can be configured with limit overrides using
`limit=` appended after the task:

```console
$ task play:bootstrap limit=proxmox-01.services.kub3.uk
task: [bootstrap] ansible-playbook \
  --syntax-check plays/bootstrap.yaml
...
```

[play-bootstrap]: https://github.com/n3tuk/ansible/blob/main/plays/bootstrap.yaml
[play-baseline]: https://github.com/n3tuk/ansible/blob/main/plays/baseline.yaml
[play-update]: https://github.com/n3tuk/ansible/blob/main/plays/update.yaml
[play-upgrade]: https://github.com/n3tuk/ansible/blob/main/plays/upgrade.yaml
[play-users]: https://github.com/n3tuk/ansible/blob/main/plays/upgrade.yaml
[taskfile]: https://github.com/n3tuk/ansible/blob/main/Taskfile.yaml
[inventory]: https://github.com/n3tuk/ansible/blob/main/inventory.yaml

## Roles

| Role                                        | Description                                                                                                                                                                                                   |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`filesystems`][role-filesystems]           | A role to configure physical partitions and filesystems, as well as physical volumes, volume groups, and logical volumes under LVM too, with support for encrypted physical filesystems with `cryptsetup`.    |
| [`bootstrap`][role-bootstrap]               | A role to bootstrap an Arch Linux installation under a configured mount point, usually set up with [`filesystems`][role-filesystems] above.                                                                   |
| [`networking`][role-networking]             | A role to configure the networking on Proxmox hosts, setting up the Thunderbolt mesh network between all the hosts in a Cluster, as well as necessary settings for Proxmox itself.                            |
| [`issue`][role-issue]                       | A role to configure `/etc/issue` on the system to describe the host and display an access warning message.                                                                                                    |
| [`ca`][role-ca]                             | A role to install the n3t.uk Root Certificate Authoritiy certificate into the the trusted store on each system, allowing tools and utilities to trust certificates issued under it.                           |
| [`kernels`][role-kernels]                   | A role to install selected Linux kernels and configure them for booting on this system.                                                                                                                       |
| [`systemd`][role-systemd]                   | A role to update the local configuration for `systemd` on this system, including `systemd` itself, `systemd-oomd`, and `systemd-timesyncd` for NTP support.                                                   |
| [`systemd_networkd`][role-systemd-networkd] | A role to enable `systemd-networkd` and install the required configuration for the local ethernet port, as well as any VLANs and Bridges required for virtual machine access to the network.                  |
| [`systemd_resolved`][role-systemd-resolved] | A role to enable `systemd-resolved` for local DNS resolution, including setting up the stub resolver, and configuring the DNS settings for this system.                                                       |
| [`firewalld`][role-firewalld]               | A role to enable `firewalls` for local firewall management, including setting up default zones and rules for this system.                                                                                     |
| [`bird`][role-bird]                         | A role to enable `bird` for local dynamic routing management using iBGP.                                                                                                                                      |
| [`starship`][role-starship]                 | A role to install and configure `starship` as a command-line prompt management utility, and allow it to clearly define the use and purpose of the system in both [`file`][role-fish] and [`bash`][role-bash]. |
| [`fish`][role-fish]                         | A role to install and configure `fish` with some basic settings and to run `starship` for users.                                                                                                              |
| [`bash`][role-bash]                         | A role to install and configure `bash` with some basic settings and to run `starship` for users.                                                                                                              |
| [`sudo`][role-sudo]                         | A role to install and configure `sudo` on this system with standadised defaults and limited access based on groups.                                                                                           |
| [`ssh`][role-ssh]                           | A role to install and configure the `ssh` service on this system to enable secure defaults and remote access for configured and supported users.                                                              |
| [`pacman`][role-pacman]                     | A role to install and configure the `pacman` utility on this system to additional Arch Linux repositories and custom settings.                                                                                |
| [`users`][role-users]                       | A role to install and configure the users and groups on the system, including the `root` user.                                                                                                                |
| [`haproxy`][role-haproxy]                   | A role to configure HAProxy on a system with standard settings, but not to configure any virtual hosts which it may serve.                                                                                    |
| [`caddy`][role-caddy]                       | A role to configure Caddy Load Balancer along with the initial virtual host for Proxmox with Cloudflare ACME certificate issuance.                                                                            |
| [`nginx`][role-nginx]                       | A role to configure nginx on a system with standard settings, but not to configure any virtual hosts which it may serve.                                                                                      |
| [`cache`][role-cache]                       | A role to configure a caching proxy virtual host in nginx which will proxy and cache Arch Linux repositories and packages.                                                                                    |
| [`logrotate`][role-logrotate]               | A role to configure logrotate with sensible defaults to support the rotation and compression of historical log files.                                                                                         |
| [`netdata`][role-netdata]                   | A role to configure Netdata either as a parent node for centralised storage and processing, or a child to collect data and stream it to a parent node.                                                        |
| [`vault`][role-vault]                       | A role to install and configure Hashicorp Vault along with associated proxies, certificates, and firewall rules.                                                                                              |
| [`tailscale`][role-tailscale]               | A role to install and configure Tailscale on a system to allow it to connect to the n3t.uk Tailscale network for secure remote access.                                                                        |
| [`ufw`][role-ufw]                           | A role to install and configure UFW (Uncomplicated Firewall) on a virtual machine to manage the firewall rules and enhance security.                                                                          |
| [`postgresql`][role-postgresql]             | A role to install and configure PostgreSQL on a virtual machine to manage relational databases.                                                                                                               |

[role-filesystems]: https://github.com/n3tuk/ansible/tree/main/roles/filesystems
[role-bootstrap]: https://github.com/n3tuk/ansible/tree/main/roles/bootstrap
[role-networking]: https://github.com/n3tuk/ansible/tree/main/roles/networking
[role-issue]: https://github.com/n3tuk/ansible/tree/main/roles/issue
[role-ca]: https://github.com/n3tuk/ansible/tree/main/roles/ca
[role-kernels]: https://github.com/n3tuk/ansible/tree/main/roles/kernels
[role-systemd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd
[role-systemd-networkd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_networkd
[role-systemd-resolved]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_resolved
[role-firewalld]: https://github.com/n3tuk/ansible/tree/main/roles/firewalld
[role-bird]: https://github.com/n3tuk/ansible/tree/main/roles/bird
[role-starship]: https://github.com/n3tuk/ansible/tree/main/roles/starship
[role-fish]: https://github.com/n3tuk/ansible/tree/main/roles/fish
[role-bash]: https://github.com/n3tuk/ansible/tree/main/roles/bash
[role-sudo]: https://github.com/n3tuk/ansible/tree/main/roles/sudo
[role-ssh]: https://github.com/n3tuk/ansible/tree/main/roles/ssh
[role-pacman]: https://github.com/n3tuk/ansible/tree/main/roles/pacman
[role-users]: https://github.com/n3tuk/ansible/tree/main/roles/users
[role-haproxy]: https://github.com/n3tuk/ansible/tree/main/roles/haproxy
[role-caddy]: https://github.com/n3tuk/ansible/tree/main/roles/caddy
[role-nginx]: https://github.com/n3tuk/ansible/tree/main/roles/nginx
[role-cache]: https://github.com/n3tuk/ansible/tree/main/roles/cache
[role-logrotate]: https://github.com/n3tuk/ansible/tree/main/roles/logrotate
[role-netdata]: https://github.com/n3tuk/ansible/tree/main/roles/netdata
[role-vault]: https://github.com/n3tuk/ansible/tree/main/roles/vault
[role-tailscale]: https://github.com/n3tuk/ansible/tree/main/roles/tailscale
[role-ufw]: https://github.com/n3tuk/ansible/tree/main/roles/ufw
[role-postgresql]: https://github.com/n3tuk/ansible/tree/main/roles/postgresql
