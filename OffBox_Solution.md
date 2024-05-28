## Topology

<img width="1198" alt="Screenshot 2024-05-28 at 8 09 31 PM" src="https://github.com/masnugro/Junos/assets/78342991/e18338ca-cad5-4b0a-914c-c4771eaaa43c">




Untrusted Network

```
set interfaces ge-0/0/0 unit 0 family inet address 10.10.10.1/30
set interfaces ge-0/0/1 unit 0 family inet address 11.11.11.1/30
set interfaces ge-0/0/2 unit 0 family inet address 12.12.12.1/30
set interfaces ge-0/0/3 unit 0 family inet address 85.0.0.2/30
set interfaces fxp0 unit 0 family inet address 100.123.1.0/16
set interfaces lo0 unit 20 family inet address 85.0.0.10/32
set policy-options policy-statement default-export term 1 from protocol static
set policy-options policy-statement default-export term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement default-export term 1 then accept
set routing-options autonomous-system 2000
set routing-options static route 0.0.0.0/0 next-hop 85.0.0.1
set routing-options static route 11.0.0.0/28 next-hop 70.1.1.2
set protocols router-advertisement interface fxp0.0 managed-configuration
set protocols bgp group vsrx1-untrust type external
set protocols bgp group vsrx1-untrust peer-as 500
set protocols bgp group vsrx1-untrust local-as 2000
set protocols bgp group vsrx1-untrust multipath
set protocols bgp group vsrx1-untrust neighbor 10.10.10.2 export default-export
set protocols bgp group vsrx-2-untrust type external
set protocols bgp group vsrx-2-untrust peer-as 500
set protocols bgp group vsrx-2-untrust local-as 2000
set protocols bgp group vsrx-2-untrust multipath
set protocols bgp group vsrx-2-untrust neighbor 11.11.11.2 export default-export
set protocols bgp group vsrx3-untrust type external
set protocols bgp group vsrx3-untrust peer-as 500
set protocols bgp group vsrx3-untrust local-as 2000
set protocols bgp group vsrx3-untrust multipath
set protocols bgp group vsrx3-untrust neighbor 12.12.12.2 export default-export
```

Trusted Network

```

set interfaces ge-0/0/0 unit 0 family inet address 13.13.13.1/30
set interfaces ge-0/0/1 unit 0 family inet address 14.14.14.1/30
set interfaces ge-0/0/2 unit 0 family inet address 15.15.15.1/30
set interfaces ge-0/0/3 unit 0 family inet address 70.1.1.1/30
set interfaces fxp0 unit 0 family inet address 100.123.1.1/16
set interfaces lo0 unit 10 family inet address 12.0.0.1/32
set interfaces lo0 unit 10 family inet address 12.0.0.2/32
set interfaces lo0 unit 10 family inet address 12.0.0.3/32
set policy-options policy-statement client-export term 1 from protocol direct
set policy-options policy-statement client-export term 1 from route-filter 12.0.0.0/24 orlonger
set policy-options policy-statement client-export term 1 then accept
set policy-options policy-statement client-export term 2 from protocol static
set policy-options policy-statement client-export term 2 from route-filter 200.0.0.0/24 orlonger
set policy-options policy-statement client-export term 2 then accept
set policy-options policy-statement default-export term 1 from protocol static
set policy-options policy-statement default-export term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement default-export term 1 then accept
set policy-options policy-statement pfe-hash from route-filter 100.0.0.1/32 exact
set policy-options policy-statement pfe-hash from route-filter 0.0.0.0/0 exact
set policy-options policy-statement pfe-hash then load-balance consistent-hash
set policy-options policy-statement pfe-hash then accept
set policy-options policy-statement pfe-lb-hash term 1 from route-filter 100.0.0.1/32 exact
set policy-options policy-statement pfe-lb-hash term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement pfe-lb-hash term 1 then load-balance source-ip-only
set policy-options policy-statement pfe-lb-hash term 1 then accept
set policy-options policy-statement pfe-lb-hash term 2 then load-balance per-packet
set policy-options policy-statement pfe-lb-hash term 2 then accept
set routing-options autonomous-system 1000
set routing-options static route 11.0.0.0/28 next-hop 70.1.1.2
set routing-options forwarding-table export pfe-lb-hash
set protocols router-advertisement interface fxp0.0 managed-configuration
set protocols bgp group vsrx1-trust type external
set protocols bgp group vsrx1-trust import pfe-hash
set protocols bgp group vsrx1-trust peer-as 500
set protocols bgp group vsrx1-trust local-as 1000
set protocols bgp group vsrx1-trust multipath
set protocols bgp group vsrx1-trust neighbor 13.13.13.2 export client-export
set protocols bgp group vsrx2-trust type external
set protocols bgp group vsrx2-trust import pfe-hash
set protocols bgp group vsrx2-trust peer-as 500
set protocols bgp group vsrx2-trust local-as 1000
set protocols bgp group vsrx2-trust multipath
set protocols bgp group vsrx2-trust neighbor 14.14.14.2 export client-export
set protocols bgp group vsrx3-trust type external
set protocols bgp group vsrx3-trust import pfe-hash
set protocols bgp group vsrx3-trust peer-as 500
set protocols bgp group vsrx3-trust local-as 1000
set protocols bgp group vsrx3-trust multipath
set protocols bgp group vsrx3-trust neighbor 15.15.15.2 export client-export
```

vSRX-1

```

set security nat source pool vsrx1-pool address 30.0.0.0/30
set security nat source pool vsrx1-pool port range 2048
set security nat source pool vsrx1-pool port range to 2999
set security nat source rule-set vsrx1-nat-rule from zone trust
set security nat source rule-set vsrx1-nat-rule to zone untrust
set security nat source rule-set vsrx1-nat-rule rule vsrx-nat-rule1 match source-address 12.0.0.0/8
set security nat source rule-set vsrx1-nat-rule rule vsrx-nat-rule1 match destination-address 0.0.0.0/0
set security nat source rule-set vsrx1-nat-rule rule vsrx-nat-rule1 match application any
set security nat source rule-set vsrx1-nat-rule rule vsrx-nat-rule1 then source-nat pool vsrx1-pool
set security policies from-zone trust to-zone trust policy default-permit match source-address any
set security policies from-zone trust to-zone trust policy default-permit match destination-address any
set security policies from-zone trust to-zone trust policy default-permit match application any
set security policies from-zone trust to-zone trust policy default-permit then permit
set security policies from-zone trust to-zone untrust policy default-permit match source-address any
set security policies from-zone trust to-zone untrust policy default-permit match destination-address any
set security policies from-zone trust to-zone untrust policy default-permit match application any
set security policies from-zone trust to-zone untrust policy default-permit then permit
set security policies from-zone trust to-zone untrust policy NAPT44-policy match source-address NAPT-source
set security policies from-zone trust to-zone untrust policy NAPT44-policy match destination-address any
set security policies from-zone trust to-zone untrust policy NAPT44-policy match application any
set security policies from-zone trust to-zone untrust policy NAPT44-policy then permit
set security zones security-zone trust host-inbound-traffic system-services all
set security zones security-zone trust host-inbound-traffic protocols all
set security zones security-zone trust interfaces ge-0/0/1.0
set security zones security-zone trust interfaces lo0.0
set security zones security-zone untrust host-inbound-traffic system-services all
set security zones security-zone untrust host-inbound-traffic protocols all
set security zones security-zone untrust interfaces ge-0/0/0.0
set interfaces ge-0/0/0 unit 0 family inet address 10.10.10.2/30
set interfaces ge-0/0/1 unit 0 family inet address 13.13.13.2/30
set interfaces fxp0 unit 0 family inet dhcp
set interfaces lo0 unit 0 family inet address 100.0.0.1/32
set policy-options policy-statement trust-policy term 1 from protocol bgp
set policy-options policy-statement trust-policy term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement trust-policy term 1 then next-hop self
set policy-options policy-statement trust-policy term 1 then accept
set policy-options policy-statement trust-policy term 2 from protocol direct
set policy-options policy-statement trust-policy term 2 from route-filter 100.0.0.1/32 exact
set policy-options policy-statement trust-policy term 2 then accept
set policy-options policy-statement trust-policy term 3 then reject
set policy-options policy-statement untrust-policy term 1 from protocol static
set policy-options policy-statement untrust-policy term 1 from route-filter 30.0.0.0/30 exact
set policy-options policy-statement untrust-policy term 1 from route-filter 10.0.0.0/8 orlonger
set policy-options policy-statement untrust-policy term 1 then next-hop self
set policy-options policy-statement untrust-policy term 1 then accept
set policy-options policy-statement untrust-policy term 2 then reject
set protocols bgp group MX-INT type external
set protocols bgp group MX-INT export trust-policy
set protocols bgp group MX-INT local-as 500
set protocols bgp group MX-INT neighbor 13.13.13.1 peer-as 1000
set protocols bgp group MX-INET type external
set protocols bgp group MX-INET export untrust-policy
set protocols bgp group MX-INET local-as 500
set protocols bgp group MX-INET neighbor 10.10.10.1 peer-as 2000
set routing-options autonomous-system 500
set routing-options static route 30.0.0.0/30 discard
```

vSRX-2

```
set security nat source pool vsrx2-pool address 40.0.0.0/30
set security nat source pool vsrx2-pool port range 3000
set security nat source pool vsrx2-pool port range to 3999
set security nat source rule-set vsrx2-rule from zone trust
set security nat source rule-set vsrx2-rule to zone untrust
set security nat source rule-set vsrx2-rule rule vsrx2-rule match source-address 12.0.0.0/8
set security nat source rule-set vsrx2-rule rule vsrx2-rule match destination-address 0.0.0.0/0
set security nat source rule-set vsrx2-rule rule vsrx2-rule match application any
set security nat source rule-set vsrx2-rule rule vsrx2-rule then source-nat pool vsrx2-pool
set security policies from-zone trust to-zone trust policy default-permit match source-address any
set security policies from-zone trust to-zone trust policy default-permit match destination-address any
set security policies from-zone trust to-zone trust policy default-permit match application any
set security policies from-zone trust to-zone trust policy default-permit then permit
set security policies from-zone trust to-zone untrust policy default-permit match source-address any
set security policies from-zone trust to-zone untrust policy default-permit match destination-address any
set security policies from-zone trust to-zone untrust policy default-permit match application any
set security policies from-zone trust to-zone untrust policy default-permit then permit
set security zones security-zone trust host-inbound-traffic system-services all
set security zones security-zone trust host-inbound-traffic protocols all
set security zones security-zone trust interfaces ge-0/0/1.0
set security zones security-zone trust interfaces lo0.0
set security zones security-zone untrust host-inbound-traffic system-services all
set security zones security-zone untrust host-inbound-traffic protocols all
set security zones security-zone untrust interfaces ge-0/0/0.0
set interfaces ge-0/0/0 unit 0 family inet address 11.11.11.2/30
set interfaces ge-0/0/1 unit 0 family inet address 14.14.14.2/30
set interfaces fxp0 unit 0 family inet dhcp
set interfaces lo0 unit 0 family inet address 100.0.0.1/32
set policy-options policy-statement trust-policy term 1 from protocol bgp
set policy-options policy-statement trust-policy term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement trust-policy term 1 then next-hop self
set policy-options policy-statement trust-policy term 1 then accept
set policy-options policy-statement trust-policy term 2 from protocol direct
set policy-options policy-statement trust-policy term 2 from route-filter 100.0.0.1/32 exact
set policy-options policy-statement trust-policy term 2 then accept
set policy-options policy-statement trust-policy term 3 then reject
set policy-options policy-statement untrust-policy term 1 from protocol static
set policy-options policy-statement untrust-policy term 1 from route-filter 40.0.0.0/30 exact
set policy-options policy-statement untrust-policy term 1 from route-filter 11.0.0.0/8 orlonger
set policy-options policy-statement untrust-policy term 1 then next-hop self
set policy-options policy-statement untrust-policy term 1 then accept
set policy-options policy-statement untrust-policy term 2 then reject
set protocols bgp group MX-internal type external
set protocols bgp group MX-internal export trust-policy
set protocols bgp group MX-internal local-as 500
set protocols bgp group MX-internal neighbor 14.14.14.1 peer-as 1000
set protocols bgp group MX-INET type external
set protocols bgp group MX-INET export untrust-policy
set protocols bgp group MX-INET local-as 500
set protocols bgp group MX-INET neighbor 11.11.11.1 peer-as 2000
set routing-options autonomous-system 500
set routing-options static route 40.0.0.0/30 discard
```

vSRX-3

```
set security nat source pool vsrx3-pool address 50.0.0.0/30
set security nat source pool vsrx3-pool port range 4000
set security nat source pool vsrx3-pool port range to 4999
set security nat source rule-set vsrx3-nat from zone trust
set security nat source rule-set vsrx3-nat to zone untrust
set security nat source rule-set vsrx3-nat rule vsrx3-rule match source-address 12.0.0.0/8
set security nat source rule-set vsrx3-nat rule vsrx3-rule match destination-address 0.0.0.0/0
set security nat source rule-set vsrx3-nat rule vsrx3-rule match application any
set security nat source rule-set vsrx3-nat rule vsrx3-rule then source-nat pool vsrx3-pool
set security policies from-zone trust to-zone trust policy default-permit match source-address any
set security policies from-zone trust to-zone trust policy default-permit match destination-address any
set security policies from-zone trust to-zone trust policy default-permit match application any
set security policies from-zone trust to-zone trust policy default-permit then permit
set security policies from-zone trust to-zone untrust policy default-permit match source-address any
set security policies from-zone trust to-zone untrust policy default-permit match destination-address any
set security policies from-zone trust to-zone untrust policy default-permit match application any
set security policies from-zone trust to-zone untrust policy default-permit then permit
set security zones security-zone trust host-inbound-traffic system-services all
set security zones security-zone trust host-inbound-traffic protocols all
set security zones security-zone trust interfaces ge-0/0/1.0
set security zones security-zone trust interfaces lo0.0
set security zones security-zone untrust screen untrust-screen
set security zones security-zone untrust host-inbound-traffic system-services all
set security zones security-zone untrust host-inbound-traffic protocols all
set security zones security-zone untrust interfaces ge-0/0/0.0
set interfaces ge-0/0/0 unit 0 family inet address 12.12.12.2/30
set interfaces ge-0/0/1 unit 0 family inet address 15.15.15.2/30
set interfaces fxp0 unit 0 family inet dhcp
set interfaces lo0 unit 0 family inet address 100.0.0.1/32
set policy-options policy-statement trust-policy term 1 from protocol bgp
set policy-options policy-statement trust-policy term 1 from route-filter 0.0.0.0/0 exact
set policy-options policy-statement trust-policy term 1 then next-hop self
set policy-options policy-statement trust-policy term 1 then accept
set policy-options policy-statement trust-policy term 2 from protocol direct
set policy-options policy-statement trust-policy term 2 from route-filter 100.0.0.1/32 exact
set policy-options policy-statement trust-policy term 2 then accept
set policy-options policy-statement trust-policy term 3 then reject
set policy-options policy-statement untrust-policy term 1 from protocol static
set policy-options policy-statement untrust-policy term 1 from route-filter 50.0.0.0/30 exact
set policy-options policy-statement untrust-policy term 1 from route-filter 12.0.0.0/8 orlonger
set policy-options policy-statement untrust-policy term 1 then next-hop self
set policy-options policy-statement untrust-policy term 1 then accept
set policy-options policy-statement untrust-policy term 2 then reject
set protocols bgp group MX-internal type external
set protocols bgp group MX-internal export trust-policy
set protocols bgp group MX-internal local-as 500
set protocols bgp group MX-internal neighbor 15.15.15.1 peer-as 1000
set protocols bgp group MX-INET type external
set protocols bgp group MX-INET export untrust-policy
set protocols bgp group MX-INET local-as 500
set protocols bgp group MX-INET neighbor 12.12.12.1 peer-as 2000
set routing-options autonomous-system 500
set routing-options static route 50.0.0.0/30 discard
```




