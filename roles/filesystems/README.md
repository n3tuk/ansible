# n3t.uk filesystems Ansible Role

An Ansible role for the creation and wiping of filesystems, and preparation of
the `/etc/fstab` and `/etc/crypttab{,.initramfs}` files on Arch Linux systems
during initial installation of the physical or virtual machine.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                         | Default | Description                                                                                                             |
| :------------------------------- | :------ | :---------------------------------------------------------------------------------------------------------------------- |
| `filesystems_wipe_enable`        | `false` | A configuration parameter to enable the wiping of all existing volumes and partitions on the system before re-creating. |
| `filesystems_tmpfs_size`         | `256M`  | The maximum size of the `tmpfs` filesystem mounted under `/tmp` for the new system.                                     |
| `filesystems_physical_drives`    | `[]`    | A list of the physical drives on the system which should be managed by this role.                                       |
| `filesystems_physical_partition` | `[]`    | A list of the configuration of physical paritions and drives they should be set up on.                                  |
| `filesystems_volume_groups`      | `[]`    | A list of the LVM volume groups and the physical partitions they will be set up with.                                   |
| `filesystems_logical_volumes`    | `[]`    | A llist of the LVM logical volumes and the volume groups they will be set up within.                                    |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: (Re)Create all filesystems on the host
  hosts: all
  remote_user: root
  vars:
    filesystems_wipe_enable: true
  roles:
    - role: filesystems
```
