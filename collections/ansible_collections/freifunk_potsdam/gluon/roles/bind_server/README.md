# bind_server

Installs and configures bind9 as recursive caching DNS server.

## Requirements

Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

Depends on domain configuration and the IPv6 prefixes within a domain
configuration. Bind9 will only listen on interfaces with an address in the
subnet of the defined IPv6 prefixes and also only allows queries from these
ranges. This means it will listen on the `br…` interfaces.

```yaml
domains:
  "0":
    name: "Domain 0"
    # …
    prefix4: 10.0.0.0/24
    prefix6: fd00::/64
    extra_prefixes6:
      - fd00:ff::/64
  "1":
    name: "Some other name"
    # …
    prefix4: 10.0.1.0/24
    prefix6: fd01::/64
```

The above configuration will create the interfaces `br0` and `br1` (see 
[batman_adv](../batman_adv/) role) with IPv6 addresses in the defined prefixes.
So bind9 will listen on the interfaces `br0` and `br1`, and will only accept
queries from the prefixes `fd00::/64`, `fd00:ff::/64` and `fd01::/64`.

## Dependencies

This role will run without any other role, but won't do much since it would not
listen on any interface(s). So it depends on the [batman_adv](../batman_adv/)
role to have configured interfaces to listen on.

## Example Playbook

```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - batman_adv
    - bind_server
  vars:
    domains:
      "0":
        name: "Domain 0"
        batman_adv_gw_bandwidth_down: 100M
        batman_adv_gw_bandwidth_up: 100M
        batman_adv_routing_algorithm: atman-iv
        prefix4: 10.0.0.0/24
        prefix6: fd00::/64
        extra_prefixes6:
          - fd00:ff::/64
      "1":
        name: "Some other name"
        atman_adv_gw_bandwidth_down: 50M
        batman_adv_gw_bandwidth_up: 50M
        batman_adv_routing_algorithm: atman-iv
        prefix4: 10.0.1.0/24
        prefix6: fd01::/64
```
## License

CC0-1.0
