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
## Paragon Automation GUI Verification for L3VPN Service Provisioning

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
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0 --> This is the EVPN single-homed instance
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

## Paragon Automation GUI Verification for EVPN Single-homed

<img width="645" alt="image" src="https://github.com/user-attachments/assets/6f7bcff7-f1fa-4171-b8e4-653497c4eeb0">

<img width="415" alt="image" src="https://github.com/user-attachments/assets/5a6823b2-bff8-4199-929b-465360084794">

<img width="840" alt="image" src="https://github.com/user-attachments/assets/1e3a4c85-c5eb-49ef-ae0e-fb146e58f702">

## EVPN Multi-homed

```
Let's continue to the EVPN multi-homed, shall we?

I will still use the same Herndon - Durham sites for EVPN Multi-homed service provisioning with following detail:
1. We need ESI-LAG for EVPN multi-homed, hence AE (Aggregated Interface) interface is mandatory
2. I will use ge-0/0/7 as CE-facing interface (we need separate physical interface connection to bound with AE)
3. C-VLAN: 303
4. Additional configuration needed in EVPN multi-homed: create access diversity, define the LAG interface and availability redundancy mode: all-active.

Service Provisioning step-by-step from PA2.2.0 GUI:
1. Hover to the left pane and choose Orchestration -> Instance -> Add -> L2VPN
2. Upload JSON file for the intended service provisioning
3. Ensure all parameters uploaded correctly
4. Last step is to click "Save & Provision"
5. Wait until the order state becomes "Success"

This time I will use sample configuration from PE6 to look at what are all the important configuration pushed by PA2.2.0

@vMX-PE6> show configuration groups paragon-service-orchestration interfaces ge-0/0/7  
gigether-options {
    802.3ad ae2;
}

@vMX-PE6> show configuration groups paragon-service-orchestration interfaces ae2         
flexible-vlan-tagging;
encapsulation flexible-ethernet-services;
esi {
    00:23:45:67:89:ab:00:00:00:01;
    all-active;
    df-election-type {
        preference {
            value 100;
        }
    }
}
aggregated-ether-options {
    minimum-links 1;
    lacp {
        system-id 00:00:11:11:22:01;
        admin-key 100;
    }
}
unit 303 {
    encapsulation vlan-bridge;
    vlan-id 303;
    output-vlan-map swap;
}

@vMX-PE6> show configuration groups paragon-service-orchestration routing-instances 14ec68b6-accb-11ef-b5e5-5a21a950e2af 
instance-type evpn;
protocols {
    evpn {
        interface ae2.303 {
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
description "routing instance for customer EVPN-Customer and instance evpn303";
vlan-id none;
no-normalization;
interface ae2.303;
route-distinguisher 1235:7;
vrf-target target:1235:1;

EVPN multi-homed instance:

@vMX-PE6> show evpn instance 14ec68b6-accb-11ef-b5e5-5a21a950e2af extensive 
Instance: 14ec68b6-accb-11ef-b5e5-5a21a950e2af
  Route Distinguisher: 1235:7
  VLAN ID: None
  Per-instance MAC route label: 301408
  Duplicate MAC detection threshold: 5
  Duplicate MAC detection window: 180
  Duplicate MAC auto-recovery time: 2
  MAC database status                     Local  Remote
    MAC advertisements:                       0       0
    MAC+IP advertisements:                    0       0
    Default gateway MAC advertisements:       0       0
  Number of local interfaces: 2 (1 up)
    Interface name  ESI                            Mode             Status     AC-Role
    .local..13      00:00:00:00:00:00:00:00:00:00  single-homed     Up         Root 
    ae2.303         00:23:45:67:89:ab:00:00:00:01  all-active       Down       Root --> we can see all-active redundancy mode and active ESI
  Number of IRB interfaces: 0 (0 up)
  Number of protect interfaces: 0
  Number of bridge domains: 1
    VLAN  Domain-ID Intfs/up   IRB-intf  Mode            MAC-sync v4-SG-sync v6-SG-sync
    None               1  0              Extended        Enabled  Disabled   Disabled  
  Number of neighbors: 0
  Number of ethernet segments: 1
    ESI: 00:23:45:67:89:ab:00:00:00:01
      Status: Unresolved
      Local interface: ae2.303, Status: Down
      DF Election Algorithm: MOD based
      Designated forwarder: DF not elected yet
      Last designated forwarder update: Nov 27 14:23:42
      Advertised MAC label: 301440
      Advertised aliasing label: 301440
      Advertised split horizon label: 301456
  SMET Forwarding: Disabled

@vMX-PE6> show bgp summary 
Threading mode: BGP I/O
Default eBGP mode: advertise - accept, receive - accept
Groups: 4 Peers: 8 Down peers: 3
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
bgp.l3vpn.0          
                       7          7          0          0          0          0
bgp.evpn.0           
                       1          1          0          0          0          0
Peer                     AS      InPkt     OutPkt    OutQ   Flaps Last Up/Dwn State|#Active/Received/Accepted/Damped...
1.1.192.0             64220      38062      38087       0       1 1w5d 0:45:52 Establ
  bgp.l3vpn.0: 3/3/3/0
  bgp.evpn.0: 0/0/0/0
  __default_evpn__.evpn.0: 0/0/0/0
  4218852c-9dd0-11ef-b5c3-deba06bc9197.inet.0: 1/1/1/0
  3e8931aa-a00e-11ef-b5c3-deba06bc9197.evpn.0: 0/0/0/0
  0cf5ee9d-acc8-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
  14ec68b6-accb-11ef-b5e5-5a21a950e2af.evpn.0: 0/0/0/0 --> This is the EVPN multi-homed instance
  85814818-ad63-11ef-b5e5-5a21a950e2af.inet.0: 1/1/1/0
... Truncated ...
```

## Paragon Automation GUI Verification for EVPN Single-homed

<img width="645" alt="image" src="https://github.com/user-attachments/assets/662287d2-91f2-4420-b326-730e7cd04d3c">

<img width="415" alt="image" src="https://github.com/user-attachments/assets/2451f52b-5d8d-4a46-b1ec-606d10d30bfa">

<img width="380" alt="image" src="https://github.com/user-attachments/assets/70be57bf-23b7-42f9-a396-66b84309d912">

<img width="323" alt="image" src="https://github.com/user-attachments/assets/e92332a4-8ec9-41e0-bdb2-2109e5bde93c">

<img width="612" alt="image" src="https://github.com/user-attachments/assets/8490d7a5-d0da-4cdf-bf3c-e7a42135ecd6">






















