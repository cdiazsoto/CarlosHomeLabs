# Dynamic Routing with RIP v2 – Cisco Packet Tracer

##  Objective

Configure and verify **RIP version 2** to automatically exchange routing information between routers.

---

##  Real-World Scenario

Your network now includes three routers connecting multiple office sites. Manually configuring static routes on all routers has become inefficient.  
You’ll deploy **RIPv2**, a distance-vector routing protocol, to automate route sharing and improve scalability.

---

##  Devices Used

- 3 Routers (R1 – HQ, R2 – Branch, R3 – Remote)
- 3 PCs (one per LAN)
- Ethernet connections between routers
- Straight-through cables for LANs

---

##  Network Topology

```
          [HQ LAN]      [Branch LAN]       [Remote LAN]
         192.168.10.0   192.168.20.0       192.168.30.0
             PC1           PC2                 PC3
              |             |                   |
             R1 -------- R2 -------- R3
         (10.10.10.0)   (10.20.20.0)
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

##  Step 1 — Configure Router Interfaces

### On R1:
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

### On R2:
```bash
enable
configure terminal
interface g0/0
ip address 192.168.20.1 255.255.255.0
no shutdown
exit
interface g0/1
ip address 10.10.10.2 255.255.255.0
no shutdown
exit
interface g0/2
ip address 10.20.20.1 255.255.255.0
no shutdown
exit
```

### On R3:
```bash
enable
configure terminal
interface g0/0
ip address 192.168.30.1 255.255.255.0
no shutdown
exit
interface g0/1
ip address 10.20.20.2 255.255.255.0
no shutdown
exit
```

![routint](images/routint.PNG)

---

##  Step 2 — Enable RIPv2

### On All Routers:
```bash
router rip
version 2
no auto-summary
network 192.168.10.0
network 192.168.20.0
network 192.168.30.0
network 10.10.10.0
network 10.20.20.0
exit
```

 Each router advertises its directly connected networks and learns others automatically.

![ripconfig](images/ripconfig.PNG)

---

##  Step 3 — Test Connectivity

From **PC1 (192.168.10.10)**:
```bash
ping 192.168.30.10
```

 Expected result:
```
Reply from 192.168.30.10: bytes=32 time<1ms TTL=128
```

![succesfullping](images/succesfullping.PNG)

---

##  Step 4 — Verification Commands

On each router:
```bash
show ip route
show ip protocols
show running-config
```

 You should see learned routes marked with an “R”.

![iproutetable](images/iproutetable.PNG) 
![ipprotocol](images/ipprotocol.PNG)
![runconfig](images/runconfig.PNG)

---

##  Troubleshooting Scenarios

| Problem | Symptom | Fix |
|----------|----------|-----|
| Missing network in RIP config | Routes not learned | Add missing `network` command |
| Wrong IP subnet | No communication | Verify IPs on interfaces |
| Interface down | Routes not advertised | Use `no shutdown` |



---

##  Key Takeaways

- RIP v2 automates routing between routers  
- Uses hop count as a metric (max 15 hops)  
- Eliminates need for manual static routes in small networks  
- Foundation for advanced protocols like OSPF and EIGRP

---

 Packet Tracer File: `dynamic-routing-ripv2.pkt`  
 Screenshot Folder: `images/`
