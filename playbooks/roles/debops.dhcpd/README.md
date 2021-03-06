## debops.dhcpd

[![Platforms](http://img.shields.io/badge/platforms-debian%20|%20ubuntu-lightgrey.svg)](#)

Install and configure [ISC DHCP Server](https://www.isc.org/downloads/dhcp/).

### Installation

To install `debops.dhcpd` using Ansible Galaxy, run:

    ansible-galaxy install debops.dhcpd




### Role variables

List of default variables available in the inventory:

    ---
    
    # ---- Global ISC DHCP Server configuration ----
    
    # Is this DHCP server authoritative?
    dhcpd_authoritative: False
    
    # List of network interfaces to listen on for DHCP requests
    # If this list is empty, Ansible will try to guess correct interfaces
    # automatically
    dhcpd_interfaces: []
    
    # Default domain to use
    dhcpd_domain: '{{ ansible_domain }}'
    
    # List of default DNS servers. By default, point users to the same host that
    # serves them DHCP requests, on default interface. If this host is a router,
    # you might need to set DNS server to internal interface IP address.
    dhcpd_dns_servers: [ '{{ ansible_default_ipv4.address }}' ]
    
    # Max lease time in hours (default lease time is calculated below)
    dhcpd_lease_time: 24
    
    # Default global options formatted as a text block
    dhcpd_global_options: |
      option domain-name "{{ ansible_domain }}";
      option domain-name-servers {{ dhcpd_dns_servers | join(' ') }};
      default-lease-time {{ (((dhcpd_lease_time / 2) + 6) * 60 * 60)|round|int }};
      max-lease-time {{ (dhcpd_lease_time * 60 * 60)|round|int }};
      log-facility local7;
    
    # Custom options formatted as a text block
    dhcpd_options: False
    
    
    # ---- ISC DHCP Server configuration scopes ----
    
    # These lists allow you to generate nested configuration scopes in
    # dhcpd.conf. Most of the information about them can be found in dhcpd.conf(5)
    # manual page. You can create nested configuration using Ansible variable
    # expansion (examples below).
    
    # List of general configuration parameters (work in any configuration scope):
    #  - comment: ''            add a comment to a scope
    #  - options: |             custom options for that scope defined as a text block
    #  - include: ''            path to external file to include in this scope
    
    # List of hosts (works in groups, subnets):
    #  - hosts: '' or []        list of hosts to configure in that scope; if this is
    #                           a path to a file, dhcpd will include an external file
    #                           in this scope
    
    # List of parameters specific to dhcpd_classes:
    #  - class: ''              class name
    #  - subclass:              this is a hash with expression as key and additional
    #                           options as value in a text block (see example below);
    #                           each match expression must end with a colon to indicate
    #                           hash key; optional
    
    # List of parameters specific to dhcpd_groups:
    #  - subnets: []            list of subnet scopes to group together
    #  - groups: []             list of other group scopes to include. No recursion!
    
    # List of parameters specific to dhcpd_shared_networks:
    #  - name: ''               name of shared network
    #  - subnets: []            list of subnets in a shared network (do not use
    #                           dhcpd_subnets here, because they will be duplicated
    #                           and DHCP server will not start)
    
    # List of parameters specific to dhcpd_subnets:
    #  - subnet: ''             start of a subnet range (ie.: 192.168.1.0)
    #  - netmask: ''            netmask for this subnet (ie.: 255.255.255.0)
    #  - routers: '' or []      address or list of addresses of gateway for that
    #                           subnet (ie.: 192.168.1.1)
    
    # List of parameters specific to dhcpd_hosts:
    #  - hostname: ''           hostname, without domain part
    #  - address: ''            IP address reserved for that host, optional
    #  - ethernet: ''           Ethernet MAC address of this host, optional
    
    
    # List of classes
    dhcpd_classes: []
      #- class 'example-class'
      #  subclass:
      #    'match1':
      #    'match2': |
      #      # match2 options in a text block;
    
      #- class 'example-empty-class'
    
    
    # List of groups
    dhcpd_groups: []
      #- comment: 'First group'
      #  hosts: '/etc/dhcp/dhcpd-group1-hosts.conf'
      #  groups: '{{ dhcpd_group_second }}'
    
    # An example of group nesting
    #dhcpd_group_second:
    #  - comment: 'Second group'
    #    hosts: '/etc/dhcp/dhcpd-group2-hosts.conf'
    
    
    # List of shared networks
    dhcpd_shared_networks: []
      #- name: 'shared-net'
      #  comment: "Local shared network"
      #  subnets: '{{ dhcpd_subnets_local }}'
      #  options: |
      #    default-lease-time 600;
      #    max-lease-time 900;
    
    
    # List of subnets not in a shared network
    dhcpd_subnets:
      - subnet: '{{ ansible_default_ipv4.network }}'
        netmask: '{{ ansible_default_ipv4.netmask }}'
        comment: 'Generated automatically by Ansible'
    
      #- subnet: 'dead:be:ef::/64'
      #  ipv6: True
      #  routers: '10.0.10.1'
      #  comment: "Example IPv6 subnet"
      #  options: |
      #    default-lease-time 300;
      #    max-lease-time 7200;
      #
      #- subnet: '10.0.20.0'
      #  netmask: '255.255.255.0'
      #  comment: 'Ignored subnet'
    
    # An example subnets included in a shared network
    #dhcpd_subnets_local:
    #  - subnet: '10.0.30.0'
    #    netmask: '255.255.255.0'
    #    routers: [ '10.0.30.1', '10.0.30.2' ]
    #
    #  - subnet: '10.0.40.0'
    #    netmask: '255.255.255.0'
    #    routers: '19.0.40.1'
    #    options: |
    #      default-lease-time 300;
    #      max-lease-time 7200;
    #    pools:
    #      - comment: "A pool in a subnet"
    #        range: '10.0.30.10 10.0.30.20'
    
    
    # Global list of hosts in DHCP
    dhcpd_hosts: []
    #  - hostname: 'examplehost'
    #    address: '10.0.10.1'
    #    ethernet: '00:00:00:00:00:00'
    
    # Example global list of hosts read from an external file
    #dhcpd_hosts: '/etc/dhcp/dhcpd.hosts.conf'
    
    
    # List of external files to include
    dhcpd_includes: []
      #- '/etc/dhcp/example.conf'





### Authors and license

`debops.dhcpd` role was written by:

- Maciej Delmanowski - [e-mail](mailto:drybjed@gmail.com) | [Twitter](https://twitter.com/drybjed) | [GitHub](https://github.com/drybjed)


License: [GNU General Public License v3](https://tldrlegal.com/license/gnu-general-public-license-v3-(gpl-3))


***

This role is part of the [DebOps](http://debops.org/) project. README generated by [ansigenome](https://github.com/nickjj/ansigenome/).

