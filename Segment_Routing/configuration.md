CE1

```
set system host-name CE1
set interfaces ge-0/0/0 unit 0 family inet address 192.168.10.5/24
set interfaces ge-0/0/1 unit 0 family inet address 192.168.11.5/24
set interfaces fxp0 unit 0 family inet address 100.123.1.0/16
set interfaces lo0 unit 0 family inet address 20.20.20.20/32
set policy-options policy-statement adv-lo from route-filter 20.20.20.20/32 exact
set policy-options policy-statement adv-lo then accept
set routing-options autonomous-system 1111
set protocols router-advertisement interface fxp0.0 managed-configuration
set protocols bgp group ext type external
set protocols bgp group ext export adv-lo
set protocols bgp group ext peer-as 65000
set protocols bgp group ext neighbor 192.168.10.1 local-address 192.168.10.5
```
