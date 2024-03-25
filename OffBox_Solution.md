Untrusted Network

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

