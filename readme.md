Shorewall Role
=========

This role is used to configure a shorewall and shorewall6 firewall.

If you are on CentOS or Fedora, the `firewalld` service will be stopped and `firewalld` will be removed from the system. If you configure the `shorewall_configopts` or `shorewall6_configopts`, then `shorewall` and `shorewall6` will be configured. Only the files in the `_configopts` list will be managed by shorewall.

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-neoloc.shorewall-blue.svg)](https://galaxy.ansible.com/neoloc/ansible-role-shorewall/)


Requirements
------------

All included in ansible-base package.

Role Variables
--------------

There are a lot of variables to list out so I have opted for a working example, minus some private information. All the variables that match `shorewall_group_` or `shorewall6_group_` also have a corresponding `shorewall_host_` and `shorewall6_host` with identical options. The templates are setup to put the `group` rules before the `host` rules.

```yaml
shorewall_configfiles:
  - shorewall.conf
  - params
  - interfaces
  - zones
  - hosts
  - policy
  - rules

shorewall6_configfiles:
  - shorewall6.conf
  - params
  - interfaces
  - zones
  - hosts
  - policy
  - rules

shorewall_configopts:
  ip_forwarding: "keep"
  implicit_continue: "yes"
  clampmss: "no"
  startup_enabled: "yes"

shorewall6_configopts:
  ip_forwarding: keep
  implicit_continue: yes
  clampmss: no
  startup_enabled: yes

shorewall_group_params:
  - variable:
    value:
    comment:

shorewall_group_zones:
  - name: fw
    type: firewall
    comment: the local firewall
  - name: net
    type: ipv4
  - name: trust:net
    type: ipv4
    comment: trusted hosts

shorewall_group_interfaces:
  - zone: net
    interface: $NET_IF
    options: dhcp

shorewall_group_hosts:
  - zone: trust
    hosts: $NET_IF:1.2.3.100
    comment: work isp1
  - zone: trust
    hosts: $NET_IF:2.3.4.100
    comment: work isp2
  - zone: trust
    hosts: $NET_IF:3.4.5.100
    comment: someuser
  - zone: trust
    hosts: $NET_IF:10.0.0.0/22
    comment: work internal

shorewall_group_policy:
  - source: $FW
    dest: all
    policy: ACCEPT
  - source: trust
    dest: all
    policy: CONTINUE
  - source: net
    dest: $FW
    policy: DROP
    loglevel: info
  - source: all
    dest: all
    policy: REJECT
    loglevel: info

shorewall_group_rules:
  - section: new
    action: ACCEPT
    source: all
    dest: all
    proto: icmp
    dport: 8
    sport: -
    origdest: -
    comment: allow icmp pings
  - section: new
    action: ACCEPT
    source: trust
    dest: $FW
    proto: tcp
    dport: 22
    sport: -
    origdest: -
    comment: allow ssh from trusted

shorewall6_group_zones:
  - name: fw
    type: firewall
    comment: the local firewall
  - name: net
    type: ipv6
  - name: trust:net
    type: ipv6

shorewall6_group_interfaces:
  - zone: net
    interface: $NET_IF
    options: dhcp

shorewall6_group_hosts:
  - zone: trust
    hosts: $NET_IF:[2001:db8::/64]
    comment: office network
  - zone: trust
    hosts: $NET_IF:[2001:db9::/64]
    comment: home office

shorewall6_group_policy:
  - source: $FW
    dest: all
    policy: ACCEPT
  - source: trust
    dest: all
    policy: CONTINUE
  - source: net
    dest: $FW
    policy: DROP
    loglevel: info
  - source: all
    dest: all
    policy: REJECT
    loglevel: info

shorewall6_group_rules:
  - section: new
    action: ACCEPT
    source: all
    dest: all
    proto: icmp
    dport: 8
    sport: -
    origdest: -
    comment: allow icmp pings
  - section: new
    action: ACCEPT
    source: trust
    dest: $FW
    proto: tcp
    dport: 22
    sport: -
    origdest: -
    comment: allow ssh from trusted zone to the firewall
```

Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
      roles:
         - neoloc.shorewall

License
-------

See license.md

Author Information
------------------

[![Github](https://img.shields.io/badge/Github-neoloc-blue.svg)](https://github.com/neoloc)
