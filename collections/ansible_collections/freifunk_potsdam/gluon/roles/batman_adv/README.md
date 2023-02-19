# batman_adv

Configures [B.A.T.M.A.N. advanced](https://www.open-mesh.org/projects/batman-adv/wiki/Wiki) 
interface(s) via systemd-networkd for every domain in configuration.

This will create a bridge interface (`br<domain>`) and a B.A.T.M.A.N. interface
(`bat<domain>`). The B.A.T.M.A.N. interface is added to the bridge.

## Requirements

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

A domain(s) configuration is required:

```yaml
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
    batman_adv_gw_bandwidth_down: 50M
    batman_adv_gw_bandwidth_up: 50M
    batman_adv_routing_algorithm: atman-iv
    prefix4: 10.0.1.0/24
    prefix6: fd01::/64
```

If the `batman_adv_` variable is not set, defaults are used. The defaults for
`batman_adv_` match the values in domain _0_ above.

`prefix4` and `prefix6` are used to calculate the addresses, which will be 
assigned to the bridge interface.

**NOTE**: The variable `gw_number` must be set per host to generate IP 
addresses.

`gw_number: 1` will result in:
- `10.0.0.1/24` 
- `fd00::1:0:0/64`
- `fd00:ff::1:0:0/64`

## Dependencies

Currently there are not dependencies to other roles.

## Example Playbook

```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - batman_adv
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
        batman_adv_gw_bandwidth_down: 50M
        batman_adv_gw_bandwidth_up: 50M
        batman_adv_routing_algorithm: atman-iv
        prefix4: 10.0.1.0/24
        prefix6: fd01::/64
```

## License

CC0-1.0
