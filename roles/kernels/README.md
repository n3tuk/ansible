# n3t.uk Kernels Ansible Role

Install and configure the required kernels on each system as UEFI stubs,
alongside signing and direct booting via UEFI and `efibootmgr`.

## Requirements

This role expects that [`secure_boot`][secure-boot] has been run if
`kernels_secure_boot_sign` is set to `true` as the local keys for the system
must be present if the kernel is to be successfully signed.

[secure-boot]: ../secure_boot/

## Bootstrap Support

This role provides support for the bootstrapping process used to install Arch
Linux via the LiveUSB installation image to a working server, via either
physical or virtual hosts.

The `bootstrap_mount_base` variable is checked and if a directory is provided
(i.e. `/mnt`), then the role will deposit the files under that directory, or run
the command via `arch-chroot` into the new installation.

## Role Variables

| Variable                         | Default                                                                                                                  | Description                                                                                                                                             |
| :------------------------------- | :----------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `bootstrap_mount_base`           | `""`                                                                                                                     | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs.                  |
| `kernels_install`                | `["linux-lts"]`                                                                                                          | The kernels which should be installed and configured on this system.                                                                                    |
| `kernels_secure_boot_sign`       | `false`                                                                                                                  | Set whether or not the kernels should be signed for this system (usually only required on physical systems, and requires [`secure_boot`][secure-boot]). |
| `kernels_mkinitcpio_microcode`   | `false`                                                                                                                  | Whether or not to include `intel-ucode.img` with the initramfs.                                                                                         |
| `kernels_mkinitcpio_compression` | `zstd`                                                                                                                   | The compression to use for the initramfs before building the UEFI stub.                                                                                 |
| `kernels_mkinitcpio_modules`     | `[]`                                                                                                                     | The modules to be included within the initramfs when being built.                                                                                       |
| `kernels_mkinitcpio_binaries`    | `[]`                                                                                                                     | The binaries to be included within the initramfs when being built.                                                                                      |
| `kernels_mkinitcpio_files`       | `[]`                                                                                                                     | The path to files to be included within the initramfs when being built.                                                                                 |
| `kernels_mkinitcpio_hooks`       | `["base", "systemd", "keyboard", "autodetect", "modconf", "kms", "block", "sd-vconsole", "lvm2", "filesystems", "fsck"]` | The default hooks to be configured for the initramfs on the system.                                                                                     |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure the Linux kernels
  hosts: all
  remote_user: root
  roles:
    - role: kernels
```
