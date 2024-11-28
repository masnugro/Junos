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

I will upload the JSON file for L3VPN, L2VPN (EVPN) and L2Circuit in separate files

```
## L3VPN

```
I will use three sites L3VPN service provisioning with following detail:
1. The sites are between Salt Lake - Herndon - Durham (please refer to "Focus Topology" in readme file)
2. CE-facing interface is ge-0/0/5
3. VPN ID: 112 and VLAN:202

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



















