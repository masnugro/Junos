## Test and Verify PCEP communication is working in PA2.2.0

```
Below are the prerequisite:
a. Ensure all the managed nodes has the PCEP configuration
b. Ensure the PCE server communication with the nodes are in UP state
```

## PE Configuration (I will use PE2 for the sample)

```
Below is the basic configuration needed to be configured 

@vMX-PE2> show configuration protocols pcep 
disable-multipath-capability;
pce paragon {
    local-address 100.123.1.5;
    destination-ipv4-address 100.123.42.9; --> This is the PCE Server IP in PA pods
    destination-port 4189; --> TCP-based port for PCC router to communicate with PCE server via PCEP
    pce-type active stateful; 
    lsp-provisioning;
    p2mp-lsp-report-capability;
    p2mp-lsp-update-capability;
    p2mp-lsp-init-capability;
    lsp-cleanup-timer 10;
    delegation-cleanup-timeout 10;
    pce-traffic-steering;
}

Additionally, the PCEP will not work if they do not have "anything to manage" or do now know "what to manage".
Below sample configuration is needed in all the nodes.

@vMX-PE2> show configuration protocols mpls 
lsp-external-controller pccd

Or, if you want to use Segment Routing then you can use below command (both MPLS and SR pccd can co-exist in the network)

@vMX-PE2> show configuration protocols source-packet-routing
lsp-external-controller pccd

Verification:
@vMX-PE2> show path-computation-client status 

Session              Type                        Provisioning   Status         Uptime
  paragon            Stateful Active             On             Up              450074      

LSP Summary
  Total number of LSPs        : 8        
  Static LSPs                 : 8        
  Externally controlled LSPs  : 0        
  Externally provisioned LSPs : 0/16000 (current/limit)
  Orphaned LSPs               : 0        

paragon (main)
  Delegated              : 0               
  Externally provisioned : 0               

```

## Paragon Controller

```
We can check the PCE server IP address from Paragon Controller

@controller-1:~# kubectl get pods -A | grep pce
northstar                                 ns-pceserver-c9f7487d9-ndc9d                                  1/1     Running     0              14d

@controller-1:~# kubectl get services -A | grep pce
northstar       ns-pceserver      LoadBalancer   10.110.144.80    100.123.42.9 --> we will use this external-IP address for the nodes to communicate with      4189/TCP   21d

Paragon will get all the LSP tunnel data information complete with the status, record route and Bandwidth (in bps) information
```

<img width="742" alt="image" src="https://github.com/user-attachments/assets/a6ae2898-e559-4aee-a5f9-99fecf34c3dc">

```
for example, the LSP "MPLS-MESH-from-vMX-PE5-to-vMX-PE6" has 329 bps bandwidth
```

<img width="613" alt="image" src="https://github.com/user-attachments/assets/503859a3-6c55-4da7-ad34-d2a95b137be7">

```
and we get the exact same result from the node
```

<img width="612" alt="image" src="https://github.com/user-attachments/assets/d6bc3c5a-a13a-433b-a46b-70a5d002be8f">


```
























