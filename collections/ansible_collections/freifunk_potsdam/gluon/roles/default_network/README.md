# default_network

Configures the default network interface via systemd-networkd. Also ensures
systemd-networkd is enabled.

## Requirements

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

A dict with at least one item for the `[Match]` section. See [systemd.network](https://systemd.network/systemd.network.html#%5BMatch%5D%20Section%20Options)
```yaml
default_network_match:
  MACAddress: "01:23:45:67:89:ab"
  Name: eth0
```

The `[Network]` section can be configured with the following variables:
```yaml
default_network_ipv4_address: 10.0.1.1/24
default_network_ipv4_gateway: 10.0.1.255
default_network_ipv6_address: fd00::1/64
default_network_ipv6_gateway: fe80::1
default_network_dns_servers:
  - 8.8.8.8
  - 8.8.4.4
  - 2620:fe::fe
  - 2620:fe::9
```

Additional options can be set with the `default_network_network_section_extra_options`
dict:
```yaml
default_network_network_section_extra_options:
  IPv6AcceptRA: "false"
```

## Dependencies

Currently there are not dependencies to other roles.

## Example Playbook

A configuration with static IPs:
```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - default_network
  vars:
    default_network_match:
      Name: eth0
    default_network_ipv4_address: 10.0.1.1/24
    default_network_ipv4_gateway: 10.0.1.255
    default_network_ipv6_address: fd00::1/64
    default_network_ipv6_gateway: fe80::1
    default_network_dns_servers:
      - 8.8.8.8
      - 8.8.4.4
      - 2620:fe::fe
      - 2620:fe::9
```

The same can be archived using `default_network_network_section_extra_options`:
```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - default_network
  vars:
    default_network_match:
      Name: eth0
    default_network_network_section_extra_options:
      Address: 10.0.1.1/24
      Address: fd00::1/64
      Gateway: 10.0.1.255
      Gateway: fe80::1
      DNS: 8.8.8.8
      DNS: 8.8.4.4
      DNS: 2620:fe::fe
      DNS: 2620:fe::9
```

For dynamic address configuration the `default_network_network_section_extra_options`
must be used:
```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - default_network
  vars:
    default_network_match:
      Name: eth0
    default_network_network_section_extra_options:
      DHCP: "yes"
```

## License

CC0-1.0
