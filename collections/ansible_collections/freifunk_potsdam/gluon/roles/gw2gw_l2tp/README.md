# gw2gw_l2tp

Create l2tp links between gateways in inventory.

For every gateway to gateway connection and domain a l2tp interface is created.
Having two gateways in the inventory and two domains, will result in two l2tp
interfaces. Three gateways and two domains result in four l2tp interfaces.

```
<number-of-gateways>-1 * <domains>
```

The name schema of the interface is:
`l2tpg<gateway-number>t<gateway-number-dst>d<domain>`  
`l2tpg1t2d0` is the connection from gateway `1` to gateway `2` for domain `0`.

To avoid conflicts the tunnel IDs, session IDs and ports are generated based
upon the `gw_number`, remote `gw_number` and `domain`:
- l2tp
  - _TunnelId_: `200<gateway-number><gateway-number-dst>`
  - _PeerTunnelId_: `200<gateway-number-dst><gateway-number>`
  - _Remote_: `AAAA` record of `ansible_host` in inventory (destination)
  - _Local_: `ansible_default_ipv6.address`
  - _UDPSourcePort_: `200<gateway-number-dst><gateway-number>`
  - _UDPDestinationPort_: `200<gateway-number><gateway-number-dst>`
- l2tp session
  - _Name_: `l2tpg<gateway-number>t<gateway-number-dst>d<domain>`
  - _SessionId_: `1<domain><gateway-number>`
  - _PeerSessionId_: `1<domain><gateway-number-dst>`

## Requirements

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

# Role Variables

A domain(s) configuration and inventory with individual `gw_number` variable is
required. See [Example Playbook](#example-playbook) below.

**NOTES:**
- Domain(s) configuration with at least one domain
- A individual `gw_number` per host must be defined
- The `ansible_host` variable in the inventory must have an `AAAA` record

## Dependencies

A B.A.T.M.A.N. interface (`bat<domain>`), because the l2tp interface(s) is/are
added via `batctl` to the `bat<domain>`. Therefore this role depends on the role
`freifunk_potsdam.gluon.batman_adv`.

## Example Playbook

```yaml
- hosts: gateways
  collections:
    - freifunk_potsdam.gluon
  roles:
    - batman_adv
    - gw2gw_l2tp
  vars:
    domains:
      "0":
        name: "Domain 0"
        prefix4: 10.0.0.0/24
        prefix6: fd00::/64
        extra_prefixes6:
          - fd00:ff::/64
      "1":
        name: "Some other name"
        prefix4: 10.0.1.0/24
        prefix6: fd01::/64
```

Inventory:
```yaml
all:
  hosts:
    gw1:
      ansible_host: gw1.example.tld
      gw_number: 1
    gw2:
      ansible_host: gw2.example.tld
      gw_number: 2
  children:
    gateways:
      hosts:
        gw1:
        gw2:
```

## License

CC0-1.0