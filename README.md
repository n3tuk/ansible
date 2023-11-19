# n3t.uk Ansible Playbooks

An Ansible repository for the configuration of systems and resources managed by
n3tuk.

## Playbooks

| Playbook                           | `task`&nbsp;Command          | Description                                                                                                                                      |
| :--------------------------------- | :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------- |
| [`bootstrap.yaml`][play-bootstrap] | [`task bootstrap`][taskfile] | A play which will bootstrap any host listed under the bootstrap group, and is normally used for both physical nodes as well as virtual machines. |

All Ansible plays run via `task` can be configured with limit overrides using
`limit=` appended after `--`:

```console
$ task bootstrap -- limit=node-01.s.cym-south-1.kub3.uk
task: [bootstrap] ansible-playbook \
  --syntax-check plays/bootstrap.yaml
...
```

[play-bootstrap]: https://github.com/n3tuk/ansible/blob/main/plays/bootstrap.yaml
[taskfile]: https://github.com/n3tuk/ansible/blob/main/Taskfile.yaml

## Roles

| Role                                        | Description                                                                                                                                                                                                   |
| :------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [`filesystems`][role-filesystems]           | A role to configure physical partitions and filesystems, as well as physical volumes, volume groups, and logical volumes under LVM too, with support for encrypted physical filesystems with `cryptsetup`.    |
| [`bootstrap`][role-bootstrap]               | A role to bootstrap an Arch Linux installation under a configured mount point, usually set up with [`filesystems`][role-filesystems] above.                                                                   |
| [`ca`][role-ca]                             | A role to install the n3t.uk Root Certificate Authoritiy certificate into the the trusted store on each system, allowing tools and utilities to trust certificates issued under it.                           |
| [`secure_boot`][role-secure-boot]           | A role to set up the keys and UEFI firmware to support Secure Boot on physical hosts, allowing locally-built kernels to be signed for booting.                                                                |
| [`kernels`][role-kernels]                   | A role to install selected Linux kernels and configure them for booting on this system.                                                                                                                       |
| [`systemd`][role-systemd]                   | A role to update the local configuration for `systemd` on this system, including `systemd` itself, `systemd-oomd`, and `systemd-timesyncd` for NTP support.                                                   |
| [`systemd_networkd`][role-systemd-networkd] | A role to enable `systemd-networkd` and install the required configuration for the local ethernet port, as well as any VLANs and Bridges required for virtual machine access to the network.                  |
| [`systemd_resolved`][role-systemd-resolved] | A role to enable `systemd-resolved` for local DNS resolution, including setting up the stub resolver, and configuring the DNS settings for this system.                                                       |
| [`starship`][role-starship]                 | A role to install and configure `starship` as a command-line prompt management utility, and allow it to clearly define the use and purpose of the system in both [`file`][role-fish] and [`bash`][role-bash]. |
| [`fish`][role-fish]                         | A role to install and configure `fish` with some basic settings and to run `starship` for users.                                                                                                              |
| [`bash`][role-bash]                         | A role to install and configure `bash` with some basic settings and to run `starship` for users.                                                                                                              |
| [`ssh`][role-ssh]                           | A role to install and configure the `ssh` service on this system to enable secure defaults and remote access for configured and supported users.                                                              |

[role-filesystems]: https://github.com/n3tuk/ansible/tree/main/roles/filesystems
[role-bootstrap]: https://github.com/n3tuk/ansible/tree/main/roles/bootstrap
[role-ca]: https://github.com/n3tuk/ansible/tree/main/roles/ca
[role-secure-boot]: https://github.com/n3tuk/ansible/tree/main/roles/secure_boot
[role-kernels]: https://github.com/n3tuk/ansible/tree/main/roles/kernels
[role-systemd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd
[role-systemd-networkd]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_networkd
[role-systemd-resolved]: https://github.com/n3tuk/ansible/tree/main/roles/systemd_resolved
[role-starship]: https://github.com/n3tuk/ansible/tree/main/roles/starship
[role-fish]: https://github.com/n3tuk/ansible/tree/main/roles/fish
[role-bash]: https://github.com/n3tuk/ansible/tree/main/roles/bash
[role-ssh]: https://github.com/n3tuk/ansible/tree/main/roles/ssh
