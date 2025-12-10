# n3t.uk Lego Ansible Role

An Ansible role for the installation and basic configuration of the [Lego][lego]
service to enable ACME certificate issuance and renewal.

[lego]: https://go-acme.github.io/lego/

> [!note]
> This Ansible Role does not create or renew certificates. It is expected that
> individual roles or playbooks will handle the actual certificate management
> using the resources provided by this role (such as installing the
> configuration file and then setting up the systemd timer).

Lego is a Let's Encrypt client and ACME library written in Go, and was chosen
over [Certbot][certbot] specifically due to its support for [RFC8738][rfc8738] -
Automated Certificate Management Environment (ACME) IP Identifier Validation
Extension. This allows adding IP addresses to certificate requests for their
validation and addition to certificate SANs.

[certbot]: https://certbot.eff.org/
[rfc8738]: https://datatracker.ietf.org/doc/html/rfc8738

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                  | Default                                          | Description                                                                                                                        |
| :------------------------ | :----------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------- |
| `lego_acme_mode`          | `standalone`                                     | The mode Lego will use to set up the server for resolving ACME challenges. Options are `standalone` or `caddy`.                    |
| `lego_acme_server`        | `https://acme-v02.api.letsencrypt.org/directory` | The ACME server URL to use for certificate issuance and renewal. Defaults to Let's Encrypt production.                             |
| `lego_acme_email`         | `admin@{{ ansible_facts.fqdn }}`                 | The email address to use for ACME account registration for this server.                                                            |
| `lego_acme_servers_group` | `ca`                                             | The Ansible inventory group containing the ACME server(s) for port 80 access will be enabled in the firewall.                      |
| `lego_cert_key_type`      | `ec384`                                          | The default type of key to use for the certificates. Options are `rsa2048`, `rsa3072`, `rsa4096`, `rsa8192`, `ec256`, and `ec384`. |

## Dependencies

- The `ufw` role is recommended to manage the firewall rules for HTTP-01 ACME
  access to Lego.
- If `lego_mode` is set to `caddy` rather than `standalone`, the `caddy` role is
  recommended to install the Caddy web server (also ensure that
  `caddy_auto_https` is set to `disable_redirects` to avoid conflicts with
  Lego's HTTP-01 handling).

## Example Playbook

```yaml
---
- name: Install Lego
  hosts: all
  remote_user: root
  roles:
    - role: ufw
    - role: lego
---
- name: Install Lego alongside Caddy
  hosts: all
  remote_user: root
  vars:
    lego_acme_mode: caddy
  roles:
    - role: ufw
    - role: caddy
    - role: lego
```
