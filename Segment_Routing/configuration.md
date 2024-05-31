## CE1

```

set system host-name CE1
set interfaces ge-0/0/0 unit 0 family inet address 192.168.10.5/24
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 192.168.11.5/24
set interfaces fxp0 unit 0 family inet address 100.123.1.0/16
set interfaces lo0 unit 0 family inet address 20.20.20.20/32
set policy-options policy-statement adv-lo from route-filter 20.20.20.20/32 exact
set policy-options policy-statement adv-lo then accept
set routing-options autonomous-system 1111
set routing-options static route 0.0.0.0/0 next-hop 100.123.0.1
set protocols bgp group ext type external
set protocols bgp group ext family inet labeled-unicast
set protocols bgp group ext export adv-lo
set protocols bgp group ext peer-as 65000
set protocols bgp group ext neighbor 192.168.10.1 local-address 192.168.10.5

```

## PE1

```

set system host-name PE1
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 192.168.10.1/24
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.1/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.5/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 2.2.2.2/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-instances to-CE instance-type vrf
set routing-instances to-CE protocols bgp group CE type external
set routing-instances to-CE protocols bgp group CE peer-as 1111
set routing-instances to-CE protocols bgp group CE neighbor 192.168.10.5 local-address 192.168.10.1
set routing-instances to-CE interface ge-0/0/0.0
set routing-instances to-CE route-distinguisher 1:1
set routing-instances to-CE vrf-target target:1:1
set routing-instances to-CE vrf-table-label
set routing-options router-id 2.2.2.2
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 2.2.2.2
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 101
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000
set protocols ospf area 0.0.0.0 interface ge-0/0/1.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/2.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface lo0.0 passive

```

## PE2

```

set system host-name PE2
set chassis network-services enhanced-ip
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.9/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.13/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 3.3.3.3/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 3.3.3.3
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 3.3.3.3
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 2
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000
set protocols ospf area 0.0.0.0 interface ge-0/0/1.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/2.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface lo0.0 passive

```

## P1

```

set system host-name P1
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.2/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.10/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.21/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces ge-0/0/3 unit 0 family inet address 1.1.1.17/30
set interfaces ge-0/0/3 unit 0 family mpls
set interfaces ge-0/0/4 unit 0 family inet address 1.1.1.25/30
set interfaces ge-0/0/4 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 4.4.4.4/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 4.4.4.4
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 4.4.4.4
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/0.0
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols mpls interface ge-0/0/3.0
set protocols mpls interface ge-0/0/4.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 3
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000
set protocols ospf area 0.0.0.0 interface ge-0/0/0.0
set protocols ospf area 0.0.0.0 interface ge-0/0/1.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/2.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/3.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/4.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface lo0.0 passive

```

## P2

```

set system host-name P2
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.6/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.14/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.22/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces ge-0/0/3 unit 0 family inet address 1.1.1.33/30
set interfaces ge-0/0/3 unit 0 family mpls
set interfaces ge-0/0/4 unit 0 family inet address 1.1.1.29/30
set interfaces ge-0/0/4 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 5.5.5.5/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 5.5.5.5
set routing-options autonomous-system 65000
set routing-options static route 0.0.0.0/0 next-hop 100.123.0.1
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 5.5.5.5
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/0.0
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols mpls interface ge-0/0/3.0
set protocols mpls interface ge-0/0/4.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 4
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000
set protocols ospf area 0.0.0.0 interface ge-0/0/0.0
set protocols ospf area 0.0.0.0 interface ge-0/0/1.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/2.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/3.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface ge-0/0/4.0 post-convergence-lfa node-protection
set protocols ospf area 0.0.0.0 interface lo0.0 passive

```

## P3

```

set system host-name P3
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.18/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.41/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.30/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces ge-0/0/3 unit 0 family inet address 1.1.1.37/30
set interfaces ge-0/0/3 unit 0 family mpls
set interfaces ge-0/0/4 unit 0 family inet address 1.1.1.45/30
set interfaces ge-0/0/4 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 6.6.6.6/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 6.6.6.6
set routing-options autonomous-system 65000
set protocols bgp group cluster type internal
set protocols bgp group cluster local-address 6.6.6.6
set protocols bgp group cluster family inet labeled-unicast rib inet.3
set protocols bgp group cluster family inet-vpn unicast
set protocols bgp group cluster cluster 6.6.6.6
set protocols bgp group cluster neighbor 2.2.2.2
set protocols bgp group cluster neighbor 3.3.3.3
set protocols bgp group cluster neighbor 4.4.4.4
set protocols bgp group cluster neighbor 5.5.5.5
set protocols bgp group cluster neighbor 7.7.7.7
set protocols bgp group cluster neighbor 8.8.8.8
set protocols bgp group cluster neighbor 9.9.9.9
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols mpls interface ge-0/0/3.0
set protocols mpls interface ge-0/0/4.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 5
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000

```

## P4

```

set system host-name P4
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.34/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.42/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 1.1.1.26/30
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces ge-0/0/3 unit 0 family inet address 1.1.1.53/30
set interfaces ge-0/0/3 unit 0 family mpls
set interfaces ge-0/0/4 unit 0 family inet address 1.1.1.49/30
set interfaces ge-0/0/4 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 7.7.7.7/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 7.7.7.7
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 7.7.7.7
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/1.0
set protocols mpls interface ge-0/0/2.0
set protocols mpls interface ge-0/0/3.0
set protocols mpls interface ge-0/0/4.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 6
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000

```

## PE3

```

set system host-name PE3
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.38/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.50/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces ge-0/0/2 unit 0 family inet address 192.168.12.1/24
set interfaces ge-0/0/2 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 8.8.8.8/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-instances to-CE instance-type vrf
set routing-instances to-CE protocols bgp group CE type external
set routing-instances to-CE protocols bgp group CE family inet unicast
set routing-instances to-CE protocols bgp group CE peer-as 2222
set routing-instances to-CE protocols bgp group CE neighbor 192.168.12.5 local-address 192.168.12.1
set routing-instances to-CE protocols mpls interface ge-0/0/2.0
set routing-instances to-CE interface ge-0/0/2.0
set routing-instances to-CE route-distinguisher 1:1
set routing-instances to-CE vrf-target target:1:1
set routing-instances to-CE vrf-table-label
set routing-options router-id 8.8.8.8
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 8.8.8.8
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/1.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 7
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000

```

## PE4

```

set system host-name PE4
set chassis network-services enhanced-ip
set interfaces ge-0/0/0 unit 0 family inet address 1.1.1.46/30
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 1.1.1.54/30
set interfaces ge-0/0/1 unit 0 family mpls
set interfaces lo0 unit 0 family inet address 9.9.9.9/32
set policy-options policy-statement pplb term 1 then load-balance per-packet
set routing-options router-id 9.9.9.9
set routing-options autonomous-system 65000
set protocols bgp group ibgp type internal
set protocols bgp group ibgp local-address 9.9.9.9
set protocols bgp group ibgp family inet labeled-unicast rib inet.3
set protocols bgp group ibgp family inet-vpn unicast
set protocols bgp group ibgp neighbor 6.6.6.6
set protocols mpls interface ge-0/0/1.0
set protocols ospf backup-spf-options use-post-convergence-lfa maximum-labels 6
set protocols ospf backup-spf-options use-source-packet-routing
set protocols ospf traffic-engineering advertisement always
set protocols ospf source-packet-routing node-segment ipv4-index 8
set protocols ospf source-packet-routing srgb start-label 5000
set protocols ospf source-packet-routing srgb index-range 1000

```

## CE2

```

set system host-name CE2
set interfaces ge-0/0/0 unit 0 family inet address 192.168.12.5/24
set interfaces ge-0/0/0 unit 0 family mpls
set interfaces ge-0/0/1 unit 0 family inet address 192.168.13.5/24
set interfaces lo0 unit 0 family inet address 30.30.30.30/32
set policy-options policy-statement adv-lo term 1 from route-filter 30.30.30.30/32 exact
set policy-options policy-statement adv-lo term 1 then accept
set routing-options autonomous-system 2222
set protocols bgp group ext type external
set protocols bgp group ext family inet unicast
set protocols bgp group ext export adv-lo
set protocols bgp group ext peer-as 65000
set protocols bgp group ext neighbor 192.168.12.1 local-address 192.168.12.5

```
