# n3t.uk step-ca Ansible Role

An Ansible role for the installation and basic configuration of the
[step-ca][step-ca] certificate authority service, connecting with a local
PostgreSQL database backend.

[step-ca]: https://smallstep.com/docs/step-ca/about-step-ca

## Setting up the Certificate Authority

The n3t.uk Certificate Authority is structured in two parts:

1. A YubiKey 5 which holds the root certificate and private key for the
   Certificate Authority, kept offline in a secure location, and used only to
   sign the intermediate certificates, as needed.
2. Two independent servers, each hosting a dedicated configuration of the
   `step-ca` service, which holds their intermediate certificates and private
   keys on their own YubiKey 5 Nanos (passed through to the Virtual Machine in
   Proxmox), and issue certificates to clients as needed.

The open-source version of `step-ca` does not support clustering or replication,
or operating in high-availability modes. Therefore, each `step-ca` server is
it's own fully independent Certificate Authority, with its own intermediate
certificate and private key, signed by the offline root Certificate Authority on
the YubiKey 5.

It should not matter which server issues the certificate to a client, as both
servers will be trusted by clients and servers through the same root Certificate
Authority certificate.

Nonetheless, the underlying [Caddy][caddy] load balancer that operates on each
node, receiving traffic from both internal and external networks, is configured
to service traffic via `ca-01.services.n3t.uk` in the first instance. In the
event of any major issue with the first server which prevents connectivity, we
will failover to `ca-02.services.n3t.uk`. Both Cloudflare (through Argo
Tunnels) and Tailscale (through Tailscale Services) will also automatically
redirect their routing to the alternate server on connectivity issues as well.

[caddy]: https://caddyserver.com/

### Creating and Operating the Root Certificate Authority

> [!note]
> Please ensure that `openssl`, `pcscd`, `pkcs11-provider`, `opensc`, `libp11`,
> `yubico-piv-tool`, and `ykman` are installed on the system before proceeding.

If the key has been used previously for something, first, issue a `reset`
command using `ykman` to clear out all the existing keys and settings:

> [!note]
> To interact with the PIV component of the YubiKeys, the `pcscd.service` must
> be running on the system. However, this must subsequently be disabled in
> order to use GPG.

```shell
$ ykman piv reset
WARNING! This will delete all stored PIV data and restore factory settings. Proceed? [y/N]: y
Resetting PIV data...
Reset complete. All PIV data has been cleared from the YubiKey.
Your YubiKey now has the default PIN, PUK and Management Key:
        PIN:    123456
        PUK:    12345678
        Management Key: 010203040506070801020304050607080102030405060708
```

Next, set the `PIN`, `PUK`, and the Management Key credentials:

```shell
$ ykman piv access set-retries 5 5
Enter a management key [blank to use default key]:
Enter PIN: 123456
WARNING: This will reset the PIN and PUK to the factory defaults!
Set the number of PIN and PUK retry attempts to: 5 5? [y/N]: y
Number of PIN/PUK retries set.
Default PINs have been restored:
        PIN:    123456
        PUK:    12345678

$ pwgen -1s 6 1
$ ykman piv access change-pin
Enter the current PIN: 123456
Enter the new PIN:
Repeat for confirmation:
New PIN set.

$ pwgen -1s 6 1
$ ykman piv access change-puk
Enter the current PUK: 12345678
Enter the new PUK:
Repeat for confirmation:
New PUK set.

$ pwgen -1sAr ghijklmnopqrstuvwxyz 64 1
$ ykman piv change-management-key --algorithm AES256 --touch
Enter the current management key [blank to use default key]:
Enter the new management key:
Repeat for confirmation:
New management key set.
```

With the YubiKey now secured, we can generate a new key and certificate for the
Root Certificate Authority using the `yubico-piv-tool`:

> [!warning]
> At the time of writing, there is currently a limitation in the `libp11`
> library which prevents the enumeration of ED25519 keys directly on the YubiKey
> through `openssl` (see [`yubico/yubico-piv-tool#510`][issue-510] for more
> details). Therefore, we will stick with generating RSA4096 keys for the Root
> and Intermediate Certificate Authorities.

[issue-510]: https://github.com/Yubico/yubico-piv-tool/issues/510

```shell
$ yubico-piv-tool --key \
    --action generate \
    --slot 9c \
    --pin-policy always \
    --touch-policy always \
    --algorithm RSA4096 \
    --output ca-root-r1.pub \
    --key-format PEM
Enter management key:
Successfully generated a new private key.

$ yubico-piv-tool \
    --action verify-pin \
    --action selfsign-certificate \
    --slot 9c \
    --subject "/C=UK/O=n3t.uk/CN=n3t.uk/" \
    --hash SHA512 \
    --valid-days 5154 \
    --input ca-root-r1.pub \
    --output ca-root-r1.crt
Enter PIN:
Successfully verified PIN.
Successfully generated a new self signed certificate.

$ openssl x509 -in ca-root-r1.crt -noout -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            99:29:62:8f:3a:dc:a5:2e
        Signature Algorithm: sha512WithRSAEncryption
        Issuer: C=UK, O=n3t.uk, CN=n3t.uk Root CA R1
        Validity
            Not Before: Nov 21 00:00:00 2025 GMT
            Not After : Jan  1 00:00:00 2040 GMT
        Subject: C=UK, O=n3t.uk, CN=n3t.uk Root CA R1
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (4096 bit)
                Modulus:
                    ....
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                E9:8A:49:15:AC:69:6B:C1:32:09:77:D2:80:85:85:B3:A0:11:99:F0
            X509v3 Authority Key Identifier:
                0.
            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha512WithRSAEncryption
    Signature Value:
        .....
```

Finally, we can import the Root CA certificate back in to the YubiKey for safekeeping:

```shell
$ yubico-piv-tool \
    --action import-certificate \
    --slot 82 \
    --input ca-root-r1.crt
Enter management key:
Successfully imported a new certificate.

$ ykman piv info
PIV version:              5.7.1
PIN tries remaining:      5/5
PUK tries remaining:      5/5
Management key algorithm: AES256
CHUID: No data available
CCC:   No data available
Slot 9C (SIGNATURE):
  Private key type: RSA4096

Slot 82 (RETIRED1):
  Private key type: EMPTY
  Public key type:  RSA4096
  Subject DN:       CN=n3t.uk Root CA R1,O=n3t.uk,C=UK
  Issuer DN:        CN=n3t.uk Root CA R1,O=n3t.uk,C=UK
  Serial:           12303132662296684516 (0xaabd81f8b8b327e4)
  Fingerprint:      8f2aaa4b006bec27c98e466121d3e85c2fa04fc885a2dccb5fe1288df9a6e883
  Not before:       2025-11-21T00:00:00+00:00
  Not after:        2040-01-01T00:00:00+00:00
```

### Creating and Operating the Intermediate Certificate Authorities

As noted above, each `step-ca` server operates as its own independent
Intermediate Certificate Authority, with it's own YubiKey holding the private
key and certificate. Much like the Root Certificate Authority, we need to
prepare this YubiKey by securing the PIV application. Once done, we can create
the private/public key pair and prepare a Certificate Signing Request (CSR)
which we will then use the Root Certicate Authority YubiKey to sign and issue
the Intermediate Certificate.

After following the steps above to secure the YubiKey, we can generate a new
private/public key pair and create the CSR:

> [!note]
> Ensure that the `--touch-policy` is set to `never` for the intermediate CA keys,
> as we do not want to have to physically touch the YubiKey every time the
> `step-ca` service needs to sign a certificate.

```shell
$ yubico-piv-tool --key \
    --action generate \
    --slot 9c \
    --pin-policy always \
    --touch-policy never \
    --algorithm RSA4096 \
    --output ca-intermediate-c1.pub \
    --key-format PEM
Enter management key:
Successfully generated a new private key.

$ yubico-piv-tool \
    --action verify-pin \
    --action request-certificate \
    --slot 9c \
    --subject "/C=UK/O=n3t.uk/CN=n3t.uk Intermediate CA C1/" \
    --hash SHA512 \
    --input ca-intermediate-c1.pub \
    --output ca-intermediate-c1.csr \
    --key-format PEM
Enter PIN:
Successfully verified PIN.
Successfully generated a certificate request.
```

> [!note]
> The Common Name (CN) in the subject must be unique for each intermediate CA,
> to avoid conflicts when both intermediate CA certificates are presented to
> clients during certificate validation. Here, we are using the Common Name of
> `n3t.uk Intermediate CA C1` for the first intermediate CA, and therefore
> should use `n3t.uk Intermediate CA C2` for the second.

Once the CSR has been created and written to the file locally, it must be signed
by the Root Certificate Authority on the (currently) offline YubiKey. Swap back
to the YubiKey holding the Root CA, and issue the signing command:

```shell
$ OPENSSL_CONF=openssl.conf \
  PKCS11_MODULE_PATH=/usr/lib/libykcs11.so \
  openssl x509 -req  \
    -engine pkcs11 \
    -CA ca-root-r1.crt \
    -CAkeyform engine \
    -CAkey "pkcs11:object=Private key for Digital Signature;type=private" \
    -set_serial 1 \
    -extensions ca_intermediate_c1 -extfile ca-intermediate-c1.conf \
    -days 1865 \
    -sha512 \
    -in ca-intermediate-c1.csr \
    -out ca-intermediate-c1.crt
Engine "pkcs11" set.
Certificate request self-signature ok
subject=C=UK, O=n3t.uk, CN=n3t.uk Intermediate CA C1
Enter PKCS#11 token PIN for YubiKey PIV #00000000:
Enter PKCS#11 key PIN for Private key for Digital Signature:
```

Finally, re-insert the Intermediate CA YubiKey, and import the signed
certificate back on to the YubiKey:

```shell
$ yubico-piv-tool \
    --action import-certificate \
    --slot 82 \
    --input ca-root-r1.crt
Enter management key:
Successfully imported a new certificate.

$ yubico-piv-tool \
    --action import-certificate \
    --slot 83 \
    --input ca-intermediate-c1.crt
Enter management key:
Successfully imported a new certificate.

$ ykman piv info
PIV version:              5.7.4
PIN tries remaining:      5/5
PUK tries remaining:      5/5
Management key algorithm: AES256
CHUID: No data available
CCC:   No data available
Slot 9C (SIGNATURE):
  Private key type: RSA4096

Slot 82 (RETIRED1):
  Private key type: EMPTY
  Public key type:  RSA4096
  Subject DN:       CN=n3t.uk Root CA R1,O=n3t.uk,C=UK
  Issuer DN:        CN=n3t.uk Root CA R1,O=n3t.uk,C=UK
  Serial:           11036460729155495214 (0x9929628f3adca52e)
  Fingerprint:
  Not before:       2025-11-21T00:00:00+00:00
  Not after:        2040-01-01T00:00:00+00:00

Slot 83 (RETIRED2):
  Private key type: EMPTY
  Public key type:  RSA4096
  Subject DN:       CN=n3t.uk Intermediate CA C1,O=n3t.uk,C=UK
  Issuer DN:        CN=n3t.uk Root CA R1,O=n3t.uk,C=UK
  Serial:           17634661091255856397 (0xf4bae6d47eb52d0d)
  Fingerprint:
  Not before:       2025-11-21T00:00:00+00:00
  Not after:        2031-01-01T00:00:00+00:00
```

All the keys are now ready to use for the `step-ca` service on each server.

## Bootstrapping PostgreSQL TLS connections

The encryption of the connections between PostgreSQL instances, and `step-ca`
and PostgreSQL are managed by `step-ca` itself. This creates a bootstrapping
situation, where the database cannot be started until `step-ca` is running, but
`step-ca` cannot start until it can connect to the database.

Therefore, if `step-ca` needs to be recreated from scratch, a number of steps
must be completed first:

1. Temporarily disable TLS connections in PostgreSQL by setting
   `postgresql_tls_enabled: false` for the hosts of the applicable group, and
   ensure that `postgresql_tls_bootstrap` is set to `true` to allow Lego to
   source the certificate for PostgreSQL.
1. Ensure that all `pg_hba.conf` entries for the `step-ca` PostgreSQL user are
   set to the `host` connection type, and not `hostssl`. (Normally they can be
   left as `host`, and `postgresql_tls_enabled`, when enabled, will
   automatically upgrade them to `hostssl` to force TLS-terminated connections.)
1. Run the playbook to create the PostgreSQL user and database for `step-ca` and
   to deploy `step-ca`. If it is fully operational, Caddy should self-serve its
   own TLS certificates through `step-ca`.
1. Re-add, if removed, or not yet set, the `lego` role to the playbook to allow
   it to obtain TLS certificates for PostgreSQL once more. Verify that
   `server.crt` and `server.key` are present is in the PostgreSQL data
   directory.
1. Re-enable `postgresql_tls_enabled` for the hosts of the applicable group.

## Requirements

None other than the Ansible Role.

## Role Variables

| Variable                                | Default                                           | Description                                                                                                                                                            |
| :-------------------------------------- | :------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `stepca_public_hostname`                | `""`                                              | (**optional**) The public hostname for the `step-ca` service for configuring Caddy and ACME.                                                                           |
| `stepca_listen_address`                 | `["0.0.0.0"]`                                     | (**optional**) The list of IP addresses to which the `step-ca` service will bind on startup.                                                                           |
| `stepca_listen_http`                    | `8080`                                            | (**optional**) The port for the `step-ca` service to listen on for HTTP traffic (needed for serving CRLs).                                                             |
| `stepca_listen_https`                   | `8443`                                            | (**optional**) The port for the `step-ca` service to listen on for HTTPS traffic.                                                                                      |
| `stepca_listen_trusted_proxy_cidrs`     | `['127.0.0.1/32', '::1/128']`                     | (**optional**) A list of trusted proxy CIDRs for the `step-ca` service to accept the `X-Forwarded-For` headers from.                                                   |
| `stepca_allowed_hosts`                  | `[]`                                              | (**optional**) A list of allowed hostnames for the `step-ca` firewall to allow requests from.                                                                          |
| `stepca_postgresql_host`                | `localhost`                                       | (**optional**) The hostname of the PostgreSQL server for the `step-ca` service to connect to.                                                                          |
| `stepca_postgresql_port`                | `5432`                                            | (**optional**) The port of the PostgreSQL server for the `step-ca` service to connect to.                                                                              |
| `stepca_postgresql_username`            |                                                   | (**required**) The username for the `step-ca` service to connect to the PostgreSQL server.                                                                             |
| `stepca_postgresql_password`            |                                                   | (**required**) The password for the `step-ca` service to connect to the PostgreSQL server.                                                                             |
| `stepca_postgresql_database`            | `stepca`                                          | (**optional**) The name of the database for the `step-ca` service to connect to on the PostgreSQL server.                                                              |
| `stepca_postgresql_options`             | `['sslmode=disable']`                             | (**optional**) A list of additional connection options for the `step-ca` service to use when connecting to PostgreSQL.                                                 |
| `stepca_postgresql_manager`             | `false`                                           | (**optional**) Set whether or not this node should manage the PostgreSQL user and database creation for the `step-ca` service in a high-availability setup.            |
| `stepca_crl_enabled`                    | `true`                                            | (**optional**) Whether to enable publishing Certificate Revocation Lists (CRLs) on the `step-ca` service.                                                              |
| `stepca_crl_cache_duration`             | `24h`                                             | (**optional**) The duration for which CRLs are allowed to be cached by downstream services before forcing an update.                                                   |
| `stepca_yubikey_reader_name`            | `Yubico YubiKey OTP+FIDO+CCID 00 00`              | (**optional**) The name of the YubiKey reader device for the `step-ca` service to use for signing certificates.                                                        |
| `stepca_yubikey_serial`                 |                                                   | (**required**) The serial number of the YubiKey device for the `step-ca` service to use for signing certificates.                                                      |
| `stepca_yubikey_slot`                   | `9c`                                              | (**optional**) The slot on the YubiKey device for the `step-ca` service to use for the Intermediate private key for signing certificates.                              |
| `stepca_yubikey_pin`                    | `123456`                                          | (**optional**) The PIN for the YubiKey device for the `step-ca` service to use to access the Intermediate private key on the PIV slot selected.                        |
| `stepca_root_certificate`               |                                                   | (**required**) The PEM-encoded Root Certificate Authority certificate for this `step-ca` service to use.                                                               |
| `stepca_intermediate_ca_id`             | `0`                                               | (**optional**) The ID of the Intermediate Certificate Authority to use for this `step-ca` service.                                                                     |
| `stepca_intermediate_certificate`       |                                                   | (**required**) The PEM-encoded Intermediate Certificate Authority certificate for the `step-ca` service to use.                                                        |
| `stepca_x509_allow_dns`                 | `['localhost']`                                   | (**optional**) A list of allowed DNS names for certificates issued by this `step-ca` service.                                                                          |
| `stepca_x509_allow_email`               | `[]`                                              | (**optional**) A list of allowed email addresses for certificates issued by this `step-ca` service.                                                                    |
| `stepca_x509_allow_ip`                  | `['::1', '127.0.0.1/32']`                         | (**optional**) A list of allowed IP addresses for certificates issued by this `step-ca` service.                                                                       |
| `stepca_x509_allow_wildcard_names`      | `false`                                           | (**optional**) Whether to allow wildcard names in certificates issued by the `step-ca` service.                                                                        |
| `stepca_acme_provisioner_name`          | `acme`                                            | (**optional**) The name of the ACME provisioner for this `step-ca` service.                                                                                            |
| `stepca_acme_trusted_roots`             | `{{ stepca_lib_path }}/example/certs/root_ca.pem` | (**optional**) The path to the PEM-encoded trusted root certificates for the ACME provisioner.                                                                         |
| `stepca_oidc_provisioner_name`          | `oidc`                                            | (**optional**) The name of the OIDC provisioner for this `step-ca` service.                                                                                            |
| `stepca_oidc_provisioner_endpoint`      |                                                   | (**required**) The OIDC provider endpoint URL for the OIDC provisioner to enable SSO authentication for this `step-ca` service.                                        |
| `stepca_oidc_provisioner_client_id`     |                                                   | (**required**) The client ID for the OIDC provisioner to enable SSO authentication for this `step-ca` service.                                                         |
| `stepca_oidc_provisioner_client_secret` |                                                   | (**required**) The client secret for the OIDC provisioner to enable SSO authentication for this `step-ca` service.                                                     |
| `stepca_oidc_provisioner_admins`        | `[]`                                              | (**optional**) A list of admin usernames for the OIDC provisioner which will be able to perform elevated functions when logged in by this OIDC provisioner.            |
| `stepca_oidc_provisioner_domains`       | `[]`                                              | (**optional**) A list of allowed email domains for users authenticating via the OIDC provisioner to restrict which users can authenticate with this `step-ca` service. |

## Dependencies

- The `ufw` role is required to manage the firewall rules for Authentik access.
- The `postgresql` role is required to provide the necessary backend services
  for `step-ca` manage its data.
- The `caddy` role is required to provide the web server and reverse proxy for
  `step-ca`, including providing Let's Encrypt TLS certificates for secure HTTPS
  access.
- The `tailscale` role is recommended to provide secure tunneling to `step-ca`
  from private networks.

## Example Playbook

```yaml
---
- name: Install step-ca
  hosts: all
  remote_user: root
  vars:
    postgresql_replication_role: standalone
  roles:
    - role: ufw
    - role: postgresql
    - role: caddy
    - role: tailscale
    - role: stepca
```
