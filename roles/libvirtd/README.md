# n3t.uk libvirtd Ansible Role

An Ansible role for the installation and configuration of the `libvirtd` service
on a system, plus configuration of Storage Pools and downloading of current
images to the central store for installing virtual machines.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                       | Default                                        | Description                                                                                                                                            |
| :----------------------------- | :--------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `libvirtd_images_dir`          | `/var/lib/libvirt/images`                      | The directory which should form the `images` pool on the system, as well as the place to download the installation images.                             |
| `libvirtd_image_arch_prefix`   | `https://mirror.bytemark.co.uk/archlinux/iso/` | The prefix of the URI to download the Arch Linux images.                                                                                               |
| `libvirtd_image_arch_checksum` | `{sha256 checksum}`                            | The SHA256 checksum of the Arch Linux installation image to download, both to check it's correct and to check the correct version has been downloaded. |
| `libvirtd_image_arch_version`  | `2023.11.01`                                   | The version of the Arch Linux installation image to downlaod to the images directory above.                                                            |
| `libvirtd_volume_groups`       | `["storage"]`                                  | A list of the LVM Volume Groups which should be configured as Storage Pools inside `libvirtd`.                                                         |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure libvirtd
  hosts: all
  become: true
  become_user: root
  roles:
    - role: libvirtd
```
