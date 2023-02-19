# fastd_server
=========

Install fastd and configures the daemon for every domain in configuration.

## Requirements
------------

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

A domain(s) configuration is required:

```yaml
domains:
  "0":
    name: "Domain 0"
    # …
    fastd_mtu: 1312
    fastd_log_level: "info"
  "1":
    name: "Some other name"
    # …
    fastd_bind_ipv4_address: "123.123.123.123"
    fastd_bind_ipv6_address: "2001:1234::ff"
    fastd_bind_ipv4_interface: eth0
    fastd_bind_ipv6_interface: eth0
    fastd_extra_binds: 
      - "123.123.123.123:100100"
      - "[2001:1234::ff]:100100"
      - "[2a00:abcd::1]:100000 interface eth1"
    fastd_secret_key: "…" 
    fastd_public_key: "…"
```

The variables in domain _0_ above are defaults and in domain _1_ optional. The
default listen port for is `10000 + <domain>`, so the above example will result
in two fastd devices; `fastd0` and `fastd1` (`fastd<domain>`), listen on port
`10000` for `fastd0` and for `fastd1` listening on`10001` and `100100` 
(`fastd_extra_binds`).

- `fastd_bind_ipv4_address` will use `ansible_default_ipv4.address` if not set
- `fastd_bind_ipv6_address` will use `ansible_default_ipv6.address` if not set
- `fastd_bind_ipv4_interface` will use `ansible_default_ipv4.interface` if not
  set
- `fastd_bind_ipv6_interface` will use `ansible_default_ipv6.interface` if not
  set
- `fastd_extra_binds` for additional binds, see
  [fastd configuration option `bind`](https://fastd.readthedocs.io/en/stable/manual/config.html#main-configuration)
  and omit `bind`
- `fastd_secret_key` and `fastd_public_key` can be used to provide an already
  existing key pair. Use [Ansible vault](https://docs.ansible.com/ansible/latest/vault_guide/index.html)
  for secrets!  
  If not set, a new key pair will be generated on the first run.

## Dependencies

Fastd expects a B.A.T.M.A.N. interface (`bat<domain>`), because the 
`fastd<domain>` is added via `batctl` to the `bat<domain>`. Therefore this role
depends on the role `freifunk_potsdam.gluon.batman_adv`.

## Example Playbook
----------------

```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - batman_adv
    - fastd_server
  vars:
    domains:
      "0":
        name: "Domain 0"
        prefix4: 10.0.0.0/24
        prefix6: fd00::/64
      "1":
        name: "Some other name"
        prefix4: 10.0.1.0/24
        prefix6: fd01::/64
        # override fastd defaults
        fastd_mtu: 1396
        fastd_log_level: "warn"
        fastd_bind_ipv4_address: "123.123.123.123"
        fastd_bind_ipv6_address: "2001:1234::ff"
        fastd_bind_ipv4_interface: eth0
        fastd_bind_ipv6_interface: eth0
        fastd_extra_binds: 
          - "123.123.123.123:100100"
          - "[2001:1234::ff]:100100"
          - "[2a00:abcd::1]:100000 interface eth1"
```

## License

CC0-1.0
