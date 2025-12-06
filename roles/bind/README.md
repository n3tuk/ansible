# n3t.uk bind Ansible Role

An Ansible role for the installation and configuration of the [`bind`][bind]
DNS server on a server with a default configuration.

[bind]: https://www.isc.org/bind/

## Zone Files

The role does not currently any manage zone files in any view for `bind` as it
is not expected that Ansible will manage these files in the long run. These
should be created and managed separately on the master server for each
view/zone, and then from there they will be automatically replicated to the
configured slave servers.

The following template should provide the minimium necessary for a zone file to
work with the default configuration provided by this role. It should be placed
in the appropriate zone file location on the master server, e.g.
`/var/named/{view}/{domain}.db}`. Also, if the `dns-01.{domain}` and
`dns-02.{domain}` nameserver entries are also inside the `{domain}` zone, add
glue records to ensure that they resolve correctly.

```plain
@                               1d IN SOA   dns-01.{domain}. {email}. (
                                    {YYYYMMDD}01 ; serial (yyyymmdd##)
                                    1h ; refresh
                                    15m ; retry
                                    1w  ; expiry
                                    1d ) ; minimum ttl

                                1h IN NS      dns-01.{domain}.
                                1h IN NS      dns-02.{domain}.

@                               5m IN A       127.0.0.1
```

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                     | Default   | Description                                                                                                                               |
| :--------------------------- | :-------- | :---------------------------------------------------------------------------------------------------------------------------------------- |
| `bind_listen_ipv4_addresses` | `["any"]` | (**optional**) A list of IPv4 addresses for `bind` to listen on, on the ports selected.                                                   |
| `bind_listen_ipv6_addresses` | `["any"]` | (**optional**) A list of IPv6 addresses for `bind` to listen on, on the ports selected .                                                  |
| `bind_port_dns`              | `53`      | (**optional**) The port for `bind` to listen on for standard, unencrypted, DNS requests.                                                  |
| `bind_forwarders`            | `[]`      | (**optional**) A list of IP addresses of upstream DNS servers for `bind` to forward requests to when it cannot resolve them itself.       |
| `bind_views`                 | `[]`      | (**optional**) A list of dictionaries containing the structure of the views and zones to create in `bind`. See below for an example.      |
| `bind_dnssec_validation`     | `true`    | (**optional**) Whether to enable DNSSEC validation in `bind` when forwarding and resolving DNS requests upstream from downstream clients. |
| `bind_rndc_key_secret`       | `""`      | (**optional**) The secret key to use for the `rndc` control channel.                                                                      |
| `bind_rndc_group_members`    | `[]`      | (**optional**) A list of users that should have access to the `rndc` control channel configuration file, and hence can use `rndc`.        |

## Dependencies

- The `ufw` role is required to manage the firewall rules for the `bind`
  service.
- The `tailscale` role is required to allow `bind` to provide its services to
  the Tailnet network

## Example Playbook

```yaml
---
- name: Install bind
  hosts: all
  remote_user: root
  vars:
    - name: example
      comment: Comment goes here...
      domains:
        - name: example.com
      cidrs:
        - 2001:db8:1::/48
        - 2001:db8:2::/48
        - 2001:db8:3::/48
      servers:
        master: dns-01.example.com
        slaves:
          - dns-02.example.com
          - dns-03.example.com
      secrets:
        transfer: key-goes-here...
        update: key-goes-here...
  roles:
    - role: ufw
    - role: tailscale
    - role: bind
```
