## Test and verify BGP-LS is working in PA2.2.0


```
There are some prerequisites to make BGP-LS works as follow:

a. Configure one of the PEs to peer BGP-LS with PA2.2.0 (in my case I will use PE5)
b. Configure "Dynamic Topology" from PA GUI and put the BGP-LS peer address and AS number then hit the "Save" button
c. Ensure you configure point-to-point interface in your IGP routing protocols (ISIS/OSPF) To get the optimal topology picture
d. (Optional): In a rarely case, you need to restart the toposerver pods from PA Kubernetes clusters
```

## PE5 Configuration and Verification

```
I am using JunOS 23.4R1.9 for all the nodes
@vMX-PE5> show version          
Hostname: vMX-PE5
Model: vmx
Junos: 23.4R1.9

@vMX-PE5> show configuration protocols bgp
group BGP-LS {
    type internal;
    local-address 100.123.1.8;
    family traffic-engineering {
        unicast;
    }
    export TE;
    allow 0.0.0.0/0;
}

set protocols bgp group BGP-LS type internal
set protocols bgp group BGP-LS local-address 100.123.1.8 --> this is management IP address for PE5
set protocols bgp group BGP-LS family traffic-engineering unicast
set protocols bgp group BGP-LS export TE
set protocols bgp group BGP-LS allow 0.0.0.0/0


@vMX-PE5> show configuration policy-options policy-statement TE 
term 1 {
    from family traffic-engineering;
    then accept;
}

set policy-options policy-statement TE term 1 from family traffic-engineering
set policy-options policy-statement TE term 1 then accept

@vMX-PE5> show bgp summary      
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 4 Peers: 8 Down peers: 2
Unconfigured peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
bgp.l3vpn.0          
                       5          5          0          0          0          0
lsdist.0             
                       0          0          0          0          0          0
bgp.evpn.0           
                       1          1          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
1.1.192.0             64220      16164      16130       0       2  5d 2:35:47 Establ
  bgp.l3vpn.0: 2/2/2/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  4218852c-9dd0-11ef-b5c3-deba06bc9197.inet.0: 1/1/1/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0
  0cf5ee9d-acc8-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0
1.1.192.1             64220      13401      13385       0       2  4d 5:44:11 Establ
  bgp.l3vpn.0: 0/0/0/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0
1.1.192.2             64220      21773      21720       0       2 6d 20:54:36 Establ
  bgp.l3vpn.0: 2/2/2/0
  bgp.evpn.0: 1/1/1/0
  __default_evpn__.evpn.0: 0/0/0/0
  4218852c-9dd0-11ef-b5c3-deba06bc9197.inet.0: 1/1/1/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 1/1/1/0
  0cf5ee9d-acc8-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0
3.3.3.0               64220       3599       3604       0       4  1d 3:19:10 Establ
  bgp.l3vpn.0: 0/0/0/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0
3.3.3.1               64220       9708       9695       0       3  3d 1:39:55 Establ
  bgp.l3vpn.0: 1/1/1/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0
  0cf5ee9d-acc8-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0
100.123.42.2          64220      16344      16416       0       0  5d 2:31:11 Establ
--> Let's focus on peer 100.123.42.2, since this is the PA peer for BGP-LS
  lsdist.0: 0/0/0/0
192.168.3.1             113          0          0       0       0    14:41:11 Active
192.168.3.1             113          0          0       0       0 2w5d 15:50:41 Connect

@vMX-PE5> show route advertising-protocol bgp 100.123.42.2 

lsdist.0: 96 destinations, 96 routes (96 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  NODE { AS:64220 ISO:0010.0119.2000.00 ISIS-L1:0 }/1216                  
*                         Self                         100        I
                IPv4 Router-ids:
                  1.1.192.0
                Area border router: No
                External router: No
                Attached: No
                Overload: No
                Hostname: vMX-PE2
                Area membership:
                  49 00 03 
                Node MSD:
                 - Type: 1, Value: 3
                 - Type: 2, Value: 16
  Prefix                  Nexthop              MED     Lclpref    AS path
  NODE { AS:64220 ISO:0010.0119.2001.00 ISIS-L1:0 }/1216                  
*                         Self                         100        I
                IPv4 Router-ids:
                  1.1.192.1
                Area border router: No
                External router: No
                Attached: No
                Overload: No
                Hostname: vMX-PE3
                Area membership:
                  49 00 03 
                Node MSD:
                 - Type: 1, Value: 3
                 - Type: 2, Value: 16

... Truncated ...

We can see that the topology information has been advertised by PE5 to PA

Do not forget to configure point-to-point protocols in every nodes in the network managed by PA
@vMX-PE5> show configuration groups p2p-protocols 
protocols {
    isis {
        interface <*> {
            point-to-point;
        }
    }
    ospf {
        area 0.0.0.0 {
            interface <*> {
                interface-type p2p;
            }
        }
    }
}

set groups p2p-protocols protocols isis interface <*> point-to-point
set groups p2p-protocols protocols ospf area 0.0.0.0 interface <*> interface-type p2p
```
## Paragon Controller

```
who has IP address 100.123.42.2 ?

You can just login into one of Paragon controller cluster:

Welcome to Juniper Paragon Automation OVA


This Controller IP: 100.123.42.1

This VM 100.123.42.1 is part of an EPIC on-prem system.
===============================================================================
Controller IP    :  100.123.42.1, 100.123.42.2, 100.123.42.3, 100.123.42.4
PAA Virtual IP   :  100.123.42.101
UI               :  https://100.123.42.100
Web Admin User   :  jcluser@juniper.net
===============================================================================
ova: 20241017_1231 
build: eop-release-2.2.0.8298.g4407b53b58

***************************************************************
                WELCOME TO PARAGON SHELL!
     You will now be able to execute Paragon CLI commands!
***************************************************************
root@controller-1>

From above Paragon CLI you can see that the owner of IP address 100.123.42.2 is Controller-2

You can also login to cRPD if you wish to do so.

root@controller-2:~# kubectl get pods -A | grep bmp
pf-74b36a89-070d-4e34-b55b-bc180df84bd1   bmp-5f589697b4-xdhrw     3/3     Running     0              20

root@controller-2:~# kubectl -n pf-74b36a89-070d-4e34-b55b-bc180df84bd1 exec -it bmp-5f589697b4-xdhrw -c  crpd -- cli

@bmp-5f589697b4-xdhrw> show configuration 
## Last commit: 2024-11-08 08:57:33 UTC by root
version 20231214.153508_builder.r1390688;
groups {
    extra {
        protocols {
            bgp {
                group northstar {
                    neighbor 100.123.1.8; --> PE5 management address
                }
            }
        }
    }
}
apply-groups extra;
routing-options {
    autonomous-system 64220;
    bmp {
        local-address 127.0.0.1;
        local-port 10003;
        connection-mode passive;
        monitor enable;
        station localhost {
            station-address 127.0.0.1;
        }
    }
    static {
        route 0.0.0.0/0 next-hop 169.254.1.1;
    }
}
protocols {
    bgp {
        group northstar {
            type internal;
            family traffic-engineering {
                unicast;
            }
            allow 0.0.0.0/0;
        }
    }
}

From above configuration you can see that the BGP is peering with PE5 IP management address 

```

## Paragon GUI
```
Insert PE5 BGP-LS peer IP address and the AS number and restart the browser.
```

<img width="612" alt="image" src="https://github.com/user-attachments/assets/b8c99ec0-2bda-4b49-adc1-8a807fa0ae72">


## Optional
```
If your PA2.2.0 does not grab the topology correctly or take longer time, you can try to restart the toposerver pods from the controller

@controller-1:~# kubectl get pods -A | grep topo
pf-74b36a89-070d-4e34-b55b-bc180df84bd1   toposerver-597849bb6d-xp4c2                                   2/2     Running     0              5d17h

@controller-1:~# kubectl delete pod -n pf-74b36a89-070d-4e34-b55b-bc180df84bd1 toposerver-597849bb6d-xp4c2

Wait for ~ 30 seconds to ensure both of the containers are up and running and then restart your PA GUI
```

















