# EIGRP and Default Routing – Cisco Packet Tracer

##  Objective

Set up and verify **EIGRP (Enhanced Interior Gateway Routing Protocol)** for dynamic route exchange, then configure a **default route** to simulate internet connectivity for unknown destinations.

---

##  Real-World Scenario

Your company has expanded its network to include multiple remote offices. To improve routing efficiency and reduce manual configuration, the IT team has decided to implement **EIGRP** across all routers.  
Additionally, a **default route** will be configured on the branch router to simulate a path to the internet through the headquarters router.

---

##  Devices Used

- 3 Routers (R1 – HQ, R2 – Branch, R3 – Remote)
- 3 PCs (one per LAN)
- Ethernet connections between routers
- Straight-through cables for LANs

---

##  Network Topology

```
          [HQ LAN]        [Branch LAN]        [Remote LAN]
        192.168.10.0     192.168.20.0        192.168.30.0
            PC1              PC2                  PC3
             |                |                    |
            R1 -------------- R2 -------------- R3
          (10.10.10.0)     (10.20.20.0)
```

 ![topology](images/topology.PNG)

---

##  IP Addressing Table

| Device | Interface | IP Address | Subnet Mask | Description |
|---------|------------|-------------|--------------|-------------|
| R1 | G0/0 | 192.168.10.1 | 255.255.255.0 | HQ LAN |
| R1 | G0/1 | 10.10.10.1 | 255.255.255.0 | Link to R2 |
| R2 | G0/0 | 192.168.20.1 | 255.255.255.0 | Branch LAN |
| R2 | G0/1 | 10.10.10.2 | 255.255.255.0 | Link to R1 |
| R2 | G0/2 | 10.20.20.1 | 255.255.255.0 | Link to R3 |
| R3 | G0/0 | 192.168.30.1 | 255.255.255.0 | Remote LAN |
| R3 | G0/1 | 10.20.20.2 | 255.255.255.0 | Link to R2 |

| PC | IP Address | Subnet Mask | Default Gateway |
|----|-------------|--------------|-----------------|
| PC1 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC3 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |

---

##  Step 1 — Configure Interfaces on All Routers

### Example for R1:
```bash
enable
configure terminal
interface g0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
interface g0/1
ip address 10.10.10.1 255.255.255.0
no shutdown
exit
```

Repeat for R2 and R3 following the IP table.

![router-interfaces](images/router-interfaces.PNG)
![rout-int-r2](images/rout-int-r2.PNG)
![rout-int-r3](images/rout-int-r3.PNG)

---

##  Step 2 — Configure EIGRP (Autonomous System 100)

### On R1:
```bash
router eigrp 100
network 192.168.10.0 0.0.0.255
network 10.10.10.0 0.0.0.255
no auto-summary
exit
```

### On R2:
```bash
router eigrp 100
network 192.168.20.0 0.0.0.255
network 10.10.10.0 0.0.0.255
network 10.20.20.0 0.0.0.255
no auto-summary
exit
```

### On R3:
```bash
router eigrp 100
network 192.168.30.0 0.0.0.255
network 10.20.20.0 0.0.0.255
no auto-summary
exit
```

 ![eigrp-config](images/eigrp-config.PNG)

---

##  Step 3 — Verify EIGRP Neighbors

```bash
show ip eigrp neighbors
```

 Expected: Each router should display its directly connected neighbor.

 ![eigrp-neighbors](images/eigrp-neighbors.PNG)

---

##  Step 4 — Verify EIGRP Routes

```bash
show ip route eigrp
```

 Routes learned through EIGRP appear with a **D** in the routing table.  
 ![eigrp-route-table](images/eigrp-route-table.PNG)

---

##  Step 5 — Test Connectivity Between PCs

From **PC1 (192.168.10.10)**:
```bash
ping 192.168.30.10
```

 Expected output:
```
Reply from 192.168.30.10: bytes=32 time<1ms TTL=128
```

![ping-success-eigrp](images/ping-success-eigrp.PNG)

---

#  Default Routing

##  Objective

Add a **default route** on the HQ router (R1) that points to the ISP router (R4), simulating internet access.
---

R4 or ISP router was prevously set up for the purpouse of this excercise to simulate internet connection with static routes previously  used.

---

##  Step 1 — Configure Default Route on R1

```bash
ip route 0.0.0.0 0.0.0.0 209.165.200.2
```

This tells R1: “Send all unknown traffic to ISP.”

 ![default-route-config](images/default-route-config.PNG)

---

##  Step 2 — Advertise Default Route via EIGRP

On **R1 (HQ Router)**:
```bash
router eigrp 100
network 192.168.10.0 0.0.0.255
network 10.10.10.0 0.0.0.255
redistribute static
exit
```

This allows R1 to share its static (default) route with other routers dynamically.

 ![eigrp-redistribute-static](images/eigrp-redistribute-staticc.PNG)

---

##  Step 3 — Verify Default Route Propagation

On R1 or R3:
```bash
show ip route
```

 You should see:
```
D*EX 0.0.0.0/0 [170/30720] via 10.10.10.1
```

 ![default-route-verification](images/default-route-verification.PNG)

---

##  Step 4 — Test External Connectivity

From **PC3 (192.168.30.10)**, simulate internet ping:
```bash
ping 8.8.8.8
```

 Expected: Reply from 209.165.200.1: Destination host unreachable.
The ping is forwarded through the default route to R1.

 ![defaultroutetest](images/default-route-test.PNG)

---

##  Troubleshooting Scenarios

| Problem | Symptom | Fix |
|----------|----------|-----|
| Missing EIGRP neighbor | No route learning | Check `network` commands and interface IPs |
| No default route learned | 0.0.0.0 missing | Verify `redistribute static` on R1 |
| Interface down | Partial connectivity | `no shutdown` on all ports |

---

##  Key Takeaways

- EIGRP is a **hybrid routing protocol** combining distance-vector and link-state features.  
- It provides faster convergence and better scalability than RIP.  
- Default routes are essential for forwarding unknown traffic (e.g., to the internet).  
- Combining static defaults with EIGRP redistribution creates dynamic flexibility.

---

 Packet Tracer File: `eigrp-and-default-route.pkt`  
 Screenshot Folder: `images/`

