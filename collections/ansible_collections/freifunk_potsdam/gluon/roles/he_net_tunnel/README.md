# he_net_tunnel

Configure a [HE.net IPv6 tunnel](https://tunnelbroker.net/) to provide IPv6 
connectivity and subnets.

## Requirements

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

- `he_net_tunnel_netdev_name`: device name, defaults: `he-ipv6`
- `he_net_tunnel_netdev_mtu`: MTU for tunnel, defaults: `1480`
- `he_net_tunnel_local`: Client IPv4 Address, IPv4 address of the device which
  initiates the connection. ``
- `he_net_tunnel_remote`: Server IPv4 Address (see [tunnelbroker.net](https://www.tunnelbroker.net/)
  webinterface)
- `he_net_tunnel_ttl`: TTL of the sit tunnel, defaults: `255`
- `he_net_tunnel_local_ipv6`: Client IPv6 Address (see [tunnelbroker.net](https://www.tunnelbroker.net/)
  webinterface)
- `he_net_tunnel_remote_ipv6`: Server IPv6 Address (see [tunnelbroker.net](https://www.tunnelbroker.net/)
  webinterface)
- `he_net_tunnel_prefixes`: IPv6 prefixes which are routed through the tunnel.
  Per default you get a `/64`, but can also assign an additional `/48` in the
  webinterface.

## Dependencies

Currently there are not dependencies to other roles.

## Example Playbook

```yaml
- hosts: gw1
  collections:
    - freifunk_potsdam.gluon
  roles:
    - batman_adv
  vars:
    he_net_tunnel_netdev_name: he-net
    he_net_tunnel_local: "{{ansible_default_ipv4.address|default(ansible_all_ipv4_addresses[0]) }}"
    he_net_tunnel_remote: 216.66.80.30

    he_net_tunnel_local_ipv6: 2001:470:ffff:ff::2/64
    he_net_tunnel_remote_ipv6: 2001:470:ffff:ff::1

    he_net_tunnel_prefixes:
      - 2001:470:ffff:ff::/64
      - 2001:470:aaaa::/48
```

## License

CC0-1.0
