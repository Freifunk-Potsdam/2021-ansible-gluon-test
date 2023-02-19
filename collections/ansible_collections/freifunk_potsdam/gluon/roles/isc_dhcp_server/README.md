# isc_dhcp_server 

Install and configures the ISC DHCP Server (IPv4 and IPv6) for every domain in 
configuration.

## Requirements

Systemd and Debian based distributions. Only tested with Ubuntu 22.04.

## Role Variables

A domain(s) configuration is required:

```yaml
domains:
  "0":
    name: "Domain 0"
    # …
    prefix4: 10.0.0.0/24
    prefix6: fd00::/64
    dhcp_v4_offset: 1024
  "1":
    name: "Some other name"
    # …
    prefix4: 10.0.1.0/24
    prefix6: fd01::/64
    extra_prefixes6:
      - fd01:ff::/64
    prefix6_dhcp: fd01:ff::/64
    dhcp_v4_offset: 256
    ipv4_next_node_offset: 255
    ipv6_next_node_offset: 65535
```

Domain _0_ above is a minimal configuration. This will result 

- ipv4
  - `prefix4`
  - `dhcp_v4_offset` * `gw_number`
  - announced gw is (internal) gw address
  - dns is always next node
- ipv6
  - `prefix6_dhcp` if set or `prefix6`
  - range calculated with gw number
  - dns servers is always next node 

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
