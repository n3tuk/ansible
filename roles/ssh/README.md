# n3t.uk SSH Server and Client Ansible Role

An Ansible role for the installation and configuration of the [OpenSSH][openssh]
service on the server, with an optimised configuration file for the server
component.

[openssh]: https://www.openssh.com/

## Requirements

None other than the Ansible Role.

## Bootstrap Support

This role provides support for the bootstrapping process used to install Arch
Linux via the LiveUSB installation image to a working server, via either
physical or virtual hosts.

The `bootstrap_mount_base` variable is checked and if a directory is provided
(i.e. `/mnt`), then the role will deposit the files under that directory, or run
the command via `arch-chroot` into the new installation.

## Role Variables

| Variable                         | Default                                                                                                                                                                                                                              | Description                                                                                                                            |
| :------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------- |
| `bootstrap_mount_base`           | `""`                                                                                                                                                                                                                                 | (**optional**) The root location of the system to install the configuration and packages into, and from which to build the UEFI stubs. |
| `ssh_server_max_startups`        | `10:30:100`                                                                                                                                                                                                                          | The configuration for the startup control for clients connecting to this SSH server.                                                   |
| `ssh_server_tcp_port`            | `22`                                                                                                                                                                                                                                 | The TCP port the SSH server should run on.                                                                                             |
| `ssh_server_permit_root_login`   | `no`                                                                                                                                                                                                                                 | Whether to permit root login via SSH.                                                                                                  |
| `ssh_server_kex_algorithms`      | `["curve25519-sha256", "curve25519-sha256@libssh.org", "diffie-hellman-group16-sha512", "diffie-hellman-group18-sha512", "diffie-hellman-group-exchange-sha256","sntrup761x25519-sha512@openssh.com"]`                               | The Key Exchange algorithms allowing on the SSH server.                                                                                |
| `ssh_server_ciphers`             | `["chacha20-poly1305@openssh.com", "aes256-gcm@openssh.com", "aes256-ctr", "rsa-sha2-512", "rsa-sha2-256"]`                                                                                                                          | The encryption ciphers allowed on the SSH server.                                                                                      |
| `ssh_server_macs`                | `["hmac-sha2-256-etm@openssh.com","hmac-sha2-512-etm@openssh.com"]`                                                                                                                                                                  | The Message Authentication Codes allowed on the SSH server.                                                                            |
| `ssh_server_host_key_algorithms` | `["ssh-ed25519", "ssh-ed25519-cert-v01@openssh.com", "sk-ssh-ed25519@openssh.com", "sk-ssh-ed25519-cert-v01@openssh.com", "rsa-sha2-512", "rsa-sha2-256", "rsa-sha2-512-cert-v01@openssh.com", "rsa-sha2-256-cert-v01@openssh.com"]` | The Host Key algorithms allowed on the SSH server.                                                                                     |

## Dependencies

None.

## Example Playbook

```yaml
---
- name: Install and configure Fish
  hosts: all
  remote_user: root
  roles:
    - role: fish
```
