# n3t.uk Ansible Playbooks

An Ansible repository for the configuration of systems and resources managed by
n3tuk.

## Playbooks

| Playbook                           | `task`&nbsp;Command     | Description                                                                                                                                      |
| :--------------------------------- | :---------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| `n/a`                              | [`ping`][taskfile]      | A task-specific command which will attempt to `ping` all hosts configured in the [`inventory`][inventory] using the standard `become` process.   |
| [`bootstrap.yaml`][play-bootstrap] | [`bootstrap`][taskfile] | A play which will bootstrap any host listed under the bootstrap group, and is normally used for both physical nodes as well as virtual machines. |
| [`baseline.yaml`][play-baseline]   | [`baseline`][taskfile]  | A play which will configure physical and virtual machines to baselined settings.                                                                 |
| [`upgrade.yaml`][play-upgrade]     | [`upgrade`][taskfile]   | A play which will run an update and upgrade of all Arch Linux packages of using `pacman`.                                                        |
| [`users.yaml`][play-users]         | [`users`][taskfile]     | A play which will run an create or update of all the users and groups on a system.                                                               |
| [`firewalld.yaml`][play-firewalld] | [`firewalld`][taskfile] | A play which will update the firewall configuration through `firewalld` on a system.                                                             |
| [`libvirtd.yaml`][play-libvirtd]   | [`libvirtd`][taskfile]  | A play which will update the configuration of `libvirtd` on a system and prepare the Storage Pools.                                              |
| [`cache.yaml`][play-cache]         | [`cache`][taskfile]     | A play which will update the configuration of caching proxies.                                                                                   |

All Ansible plays run via `task` can be configured with limit overrides using
`limit=` appended after the task:

```console
$ task bootstrap limit=node-01.s.cym-south-1.kub3.uk
task: [bootstrap] ansible-playbook \
  --syntax-check plays/bootstrap.yaml
...
```

[play-bootstrap]: https://github.com/n3tuk/ansible/blob/main/plays/bootstrap.yaml
[play-baseline]: https://github.com/n3tuk/ansible/blob/main/plays/baseline.yaml
[play-upgrade]: https://github.com/n3tuk/ansible/blob/main/plays/upgrade.yaml
[play-users]: https://github.com/n3tuk/ansible/blob/main/plays/upgrade.yaml
[play-firewalld]: https://github.com/n3tuk/ansible/blob/main/plays/firewalld.yaml
[play-libvirtd]: https://github.com/n3tuk/ansible/blob/main/plays/libvirtd.yaml
[play-cache]: https://github.com/n3tuk/ansible/blob/main/plays/cache.yaml
[taskfile]: https://github.com/n3tuk/ansible/blob/main/Taskfile.yaml
[inventory]: https://github.com/n3tuk/ansible/blob/main/inventory.yaml

## Roles

| Role                                        | Description                                                                                                                                                                                                   |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`filesystems`][role-filesystems]           | A role to configure physical partitions and filesystems, as well as physical volumes, volume groups, and logical volumes under LVM too, with support for encrypted physical filesystems with `cryptsetup`.    |
| [`bootstrap`][role-bootstrap]               | A role to bootstrap an Arch Linux installation under a configured mount point, usually set up with [`filesystems`][role-filesystems] above.                                                                   |
| [`issue`][role-issue]                       | A role to configure `/etc/issue` on the system to describe the host and display an access warning message.                                                                                                    |
| [`ca`][role-ca]                             | A role to install the n3t.uk Root Certificate Authoritiy certificate into the the trusted store on each system, allowing tools and utilities to trust certificates issued under it.                           |
| [`secure_boot`][role-secure-boot]           | A role to set up the keys and UEFI firmware to support Secure Boot on physical hosts, allowing locally-built kernels to be signed for booting.                                                                |
| [`kernels`][role-kernels]                   | A role to install selected Linux kernels and configure them for booting on this system.                                                                                                                       |
| [`systemd`][role-systemd]                   | A role to update the local configuration for `systemd` on this system, including `systemd` itself, `systemd-oomd`, and `systemd-timesyncd` for NTP support.                                                   |
| [`systemd_networkd`][role-systemd-networkd] | A role to enable `systemd-networkd` and install the required configuration for the local ethernet port, as well as any VLANs and Bridges required for virtual machine access to the network.                  |
| [`systemd_resolved`][role-systemd-resolved] | A role to enable `systemd-resolved` for local DNS resolution, including setting up the stub resolver, and configuring the DNS settings for this system.                                                       |
| [`starship`][role-starship]                 | A role to install and configure `starship` as a command-line prompt management utility, and allow it to clearly define the use and purpose of the system in both [`file`][role-fish] and [`bash`][role-bash]. |
| [`fish`][role-fish]                         | A role to install and configure `fish` with some basic settings and to run `starship` for users.                                                                                                              |
| [`bash`][role-bash]                         | A role to install and configure `bash` with some basic settings and to run `starship` for users.                                                                                                              |
| [`sudo`][role-sudo]                         | A role to install and configure `sudo` on this system with standadised defaults and limited access based on groups.                                                                                           |
| [`ssh`][role-ssh]                           | A role to install and configure the `ssh` service on this system to enable secure defaults and remote access for configured and supported users.                                                              |
| [`pacman`][role-pacman]                     | A role to install and configure the `pacman` utility on this system to additional Arch Linux repositories and custom settings.                                                                                |
| [`users`][role-users]                       | A role to install and configure the users and groups on the system, including the `root` user.                                                                                                                |
| [`firewalld`][role-firewalld]               | A role to install and configure a firewall for the system using `firewalld`.                                                                                                                                  |
| [`libvirtd`][role-libvirtd]                 | A role to install and configure `libvirtd` for the system and prepare the Storage Pools for Virtual Machines.                                                                                                 |
| [`machines`][role-machines]                 | A role to configure all the virtual machines to run on a node, alongside the storage and any other devices required by that machine.                                                                          |
| [`nginx`][role-nginx]                       | A role to configure nginx on a system with standard settings, but not to configure any virtual hosts which it may serve.                                                                                      |
| [`cache`][role-cache]                       | A role to configure a caching proxy virtual host in nginx which will proxy and cache Arch Linux repositories and packages.                                                                                    |
| [`logrotate`][role-logrotate]               | A role to configure logrotate with sensible defaults to support the rotation and compression of historical log files.                                                                                         |

[role-filesystems]: https://github.com/n3tuk/ansible/tree/main/roles/filesystems
[role-bootstrap]: https://github.com/n3tuk/ansible/tree/main/roles/bootstrap
[role-issue]: https://github.com/n3tuk/ansible/tree/main/roles/issue
[role-ca]: https://github.com/n3tuk/ansible/tree/main/roles/ca
[role-secure-boot]: https://github.com/n3tuk/ansible/tree/main/roles/secure_boot
[role-kernels]: https://github.com/n3tuk/ansible/tree/main/roles/kernels
[role-systemd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd
[role-systemd-networkd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_networkd
[role-systemd-resolved]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_resolved
[role-starship]: https://github.com/n3tuk/ansible/tree/main/roles/starship
[role-fish]: https://github.com/n3tuk/ansible/tree/main/roles/fish
[role-bash]: https://github.com/n3tuk/ansible/tree/main/roles/bash
[role-sudo]: https://github.com/n3tuk/ansible/tree/main/roles/sudo
[role-ssh]: https://github.com/n3tuk/ansible/tree/main/roles/ssh
[role-pacman]: https://github.com/n3tuk/ansible/tree/main/roles/pacman
[role-users]: https://github.com/n3tuk/ansible/tree/main/roles/users
[role-firewalld]: https://github.com/n3tuk/ansible/tree/main/roles/firewalld
[role-libvirtd]: https://github.com/n3tuk/ansible/tree/main/roles/libvirtd
[role-machines]: https://github.com/n3tuk/ansible/tree/main/roles/machines
[role-nginx]: https://github.com/n3tuk/ansible/tree/main/roles/nginx
[role-cache]: https://github.com/n3tuk/ansible/tree/main/roles/cache
[role-logrotate]: https://github.com/n3tuk/ansible/tree/main/roles/logrotate
