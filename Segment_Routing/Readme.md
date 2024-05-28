# What is this? 
This is to test L3VPN via OSPF SR-MPLS functionality in Juniper routers powered by Junos

# Topology
The lab topology as below:

<img width="1141" alt="Screenshot 2024-05-28 at 1 19 10 PM" src="https://github.com/masnugro/Junos/assets/78342991/b7c6617d-99ca-447c-922f-ccbadbc91792">

and the detail interface:

![Screenshot 2024-05-27 at 12 00 51 PM](https://github.com/masnugro/Junos/assets/78342991/12e02736-13c1-4989-864b-002751db14f9)



CE1
```
set interfaces ge-0/0/0 unit 0 family inet address 192.168.10.5/24
set interfaces ge-0/0/1 unit 0 family inet address 192.168.11.5/24
set interfaces fxp0 unit 0 family inet address 100.123.1.0/16
set interfaces lo0 unit 0 family inet address 20.20.20.20/32
set policy-options policy-statement adv-lo from route-filter 20.20.20.20/32 exact
set policy-options policy-statement adv-lo then accept
set routing-options autonomous-system 1111
set routing-options static route 0.0.0.0/0 next-hop 100.123.0.1
set protocols router-advertisement interface fxp0.0 managed-configuration
set protocols bgp group ext type external
set protocols bgp group ext export adv-lo
set protocols bgp group ext peer-as 65000
set protocols bgp group ext neighbor 192.168.10.1 local-address 192.168.10.5

```