# n3t.uk Ansible Playbooks

An Ansible repository for the configuration of systems and resources managed by
n3tuk.

## Playbooks

| Playbook                           | `task` Command               | Description                                                                                                                                      |
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

| Role                              | Description                                                                                                                                                                                                |
| :-------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`filesystems`][role-filesystems] | A role to configure physical partitions and filesystems, as well as physical volumes, volume groups, and logical volumes under LVM too, with support for encrypted physical filesystems with `cryptsetup`. |
| [`bootstrap`][role-bootstrap]     | A role to bootstrap an Arch Linux installation under a configured mount point, usually set up with [`filesystems`][role-filesystems] above.                                                                |
| [`secure_boot`][role-secure-boot] | A role to set up the keys and UEFI firmware to support Secure Boot on physical hosts, allowing locally-built kernels to be signed for booting.                                                             |
| [`kernels`][role-kernels]         | A role to install selected Linux kernels and configure them for booting on this system.                                                                                                                    |

[role-filesystems]: https://github.com/n3tuk/ansible/tree/main/roles/filesystems
[role-bootstrap]: https://github.com/n3tuk/ansible/tree/main/roles/bootstrap
[role-secure-boot]: https://github.com/n3tuk/ansible/tree/main/roles/secure_boot
[role-kernels]: https://github.com/n3tuk/ansible/tree/main/roles/kernels
