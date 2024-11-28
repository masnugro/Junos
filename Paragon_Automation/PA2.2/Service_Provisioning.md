## Test and verify all the Service Provisioning capabilities is working in PA2.2.0

```
Service Orchestration
1. L3VPN
2. L2VPN (EVPN)
    a. EVPN Single-homed
    b. EVPN Multi-homed
3. L2Circuit
```

## Prerequisite
```
To make service provisioning workflows runs smoothly, there are some prerequisites needs to be fulfilled in resource instance as follow:

1. Topology resource: This is to let PA workflows aware on which CE-facing interfaces and VLANS for IFL service resource for service provisioning orhestration from every PEs
2. VPN resource: This is to let PA workflows aware on "Community", "Route Distinguisher", "Route Target" and "VC ID" (this is specifically for L2Circuit service) resource that can be used by PA
3. L3-address resource: This is for IP P2P and IP loopback interface resource
4. L2-address resource: This is for "ESI address", "Admin key" and "System-id" resource
5. Routing resource: This is for AS number resource

I will upload the sample JSON file for L3VPN, L2VPN (EVPN) and L2Circuit in separate files

```
## L3VPN

```
I will use three sites L3VPN service provisioning with following detail:
1. The sites are between Salt Lake - Herndon - Durham (please refer to "Focus Topology" in readme file)
2. CE-facing interface is ge-0/0/5
3. VPN ID: 112 and VLAN: 202

Service Provisioning step-by-step from PA2.2.0 GUI:
1. Hover to the left pane and choose Orchestration -> Instance -> Add -> L3VPN
2. Upload JSON file for the intended service provisioning
3. Ensure all parameters uploaded correctly
4. Last step is to click "Save & Provision"
5. Wait until the order state becomes "Success"


During Service Provisioning workflows, PA will push necessary configuration to the nodes (below sample is from PE2)

@vMX-PE2> show configuration groups paragon-service-orchestration interfaces ge-0/0/5                                       
flexible-vlan-tagging;
mtu 1500;
encapsulation flexible-ethernet-services;
unit 202 {
    description "Network access to VPN 112 for customer L3VPN-Cust";
    vlan-id 202;
    family inet {
        policer {
            input 4218852c-9dd0-11ef-b5c3-deba06bc9197-SaltLakeLink1-input;
            output 4218852c-9dd0-11ef-b5c3-deba06bc9197-SaltLakeLink1-output;
        }
        address 192.168.2.2/24;
    }
}


@vMX-PE2> show configuration groups paragon-service-orchestration routing-instances 
4218852c-9dd0-11ef-b5c3-deba06bc9197 {
    instance-type vrf;
    protocols {
        bgp {
            group bgp {
                type external;
                neighbor 192.168.2.1 {
                    description "BGP neighbor 192.168.2.1 for VPN 112";
                    local-address 192.168.2.2;
                    peer-as 112;
                    as-override;
                }
            }
        }
    }
    description "Routing instance for VPN 112 for customer L3VPN-Cust";
    interface ge-0/0/5.202;
    route-distinguisher 1235:2;
    vrf-target target:1234:0;
    vrf-table-label;
}
```
## Paragon GUI Verification for L3VPN Service Provisioning

<img width="645" alt="image" src="https://github.com/user-attachments/assets/1724e586-7f09-45bc-a0fb-7959d16e68ef">

<img width="415" alt="image" src="https://github.com/user-attachments/assets/2451f52b-5d8d-4a46-b1ec-606d10d30bfa">

<img width="824" alt="image" src="https://github.com/user-attachments/assets/6bf1c72d-345c-4cec-8cf5-69c315c7c480">


## EVPN Single-homed

```
Since EVPN has two flavours, let's start with the single-homed first.
I will use two sites EVPN service provisioning with following detail:
1. The sites are between Herndon - Durham (please refer to "Focus Topology" in readme file)
2. CE-facing interface is ge-0/0/5
3. C-VLAN: 206

Service Provisioning step-by-step from PA2.2.0 GUI:
1. Hover to the left pane and choose Orchestration -> Instance -> Add -> L2VPN
2. Upload JSON file for the intended service provisioning
3. Ensure all parameters uploaded correctly
4. Last step is to click "Save & Provision"
5. Wait until the order state becomes "Success"

During Service Provisioning workflows, PA will push necessary configuration to the nodes (below sample is from PE5)

@vMX-PE5> show configuration groups paragon-service-orchestration interfaces ge-0/0/5    
flexible-vlan-tagging;
mtu 1500;
encapsulation flexible-ethernet-services;
unit 206 {
    encapsulation vlan-bridge;
    vlan-id 206;
    output-vlan-map swap;
}


@vMX-PE5> show configuration groups paragon-service-orchestration routing-instances
/* routing instance for customer EVPN-4site-Customer and instance evpn206 */
3e8931aa-a00e-11ef-b5c3-deba06bc9197 {
    instance-type evpn;
    protocols {
        evpn {
            interface ge-0/0/5.206 {
                interface-mac-limit {
                    100;
                    packet-action drop;
                }
            }
            duplicate-mac-detection {
                detection-threshold 5;
                auto-recovery-time 2;
            }
        }
    }
    description "routing instance for customer EVPN-Customer and instance evpn206";
    vlan-id none;
    no-normalization;
    interface ge-0/0/5.206;
    route-distinguisher 1234:2;
    vrf-target target:1235:0;
}

EVPN instance verification from PE5:

@vMX-PE5> show evpn instance 3e8931aa-a00e-11ef-b5c3-deba06bc9197 extensive 
Instance: 3e8931aa-a00e-11ef-b5c3-deba06bc9197
  Route Distinguisher: 1234:2
  VLAN ID: None
  Per-instance MAC route label: 300544
  Per-instance multicast route label: 300560
  Duplicate MAC detection threshold: 5
  Duplicate MAC detection window: 180
  Duplicate MAC auto-recovery time: 2
  MAC database status                     Local  Remote
    MAC advertisements:                       0       0
    MAC+IP advertisements:                    0       0
    Default gateway MAC advertisements:       0       0
  Number of local interfaces: 2 (2 up)
    Interface name  ESI                            Mode             Status     AC-Role
    .local..11      00:00:00:00:00:00:00:00:00:00  single-homed     Up         Root 
    ge-0/0/5.206    00:00:00:00:00:00:00:00:00:00  single-homed     Up         Root 
  Number of IRB interfaces: 0 (0 up)
  Number of protect interfaces: 0
  Number of bridge domains: 1
    VLAN  Domain-ID Intfs/up   IRB-intf  Mode            MAC-sync v4-SG-sync v6-SG-sync
    None               1  1              Extended        Enabled  Disabled   Disabled  
  Number of neighbors: 1
    Address               MAC    MAC+IP        AD        IM        ES Leaf-label DCI-Peer Flow-label DT2U-SID           DT2M-SID
    1.1.192.2               0         0         0         1         0                           NO  
  Number of ethernet segments: 0

@vMX-PE5> show bgp summary 
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 5 Peers: 9 Down peers: 4
Unconfigured peers: 1
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
bgp.l3vpn.0          
                       7          7          0          0          0          0
lsdist.0             
                       0          0          0          0          0          0
bgp.evpn.0           
                       1          1          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
1.1.192.0             64220      16844      16811       0       2  5d 7:43:51 Establ
  bgp.l3vpn.0: 3/3/3/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  4218852c-9dd0-11ef-b5c3-deba06bc9197.inet.0: 1/1/1/0
  **3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0**
  0cf5ee9d-acc8-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0

... Truncated ...

@vMX-PE5> show route table 3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0 

3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 2 destinations, 2 routes (2 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

3:1234:2::0::2.2.2.0/248 IM            
                   *[EVPN/170] 2w3d 00:28:07
                       Indirect
3:1234:3::0::1.1.192.2/248 IM            
                   *[BGP/170] 01:14:41, localpref 100, from 1.1.192.2
                      AS path: I, validation-state: unverified
                    >  to 25.25.25.1 via ge-0/0/0.0, label-switched-path MPLS-MESH-from-vMX-PE5-to-vMX-PE6
                       to 23.23.23.2 via ge-0/0/2.0, label-switched-path Bypass->25.25.25.1



```













