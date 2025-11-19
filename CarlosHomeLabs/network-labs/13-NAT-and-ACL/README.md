# Day 21–22: Multi-Branch NAT + ACL Lab (Final Working Version)

## Objective
Configure a multi-branch network where:
- Branch A has full Internet access.
- Branch B can communicate with Branch A but cannot access the Internet.
- NAT Overload (PAT) is done at HQ.
- ACL 100 blocks Branch B from the Internet but still allows inter-branch communication.
- ISP router simulates the Internet and includes an Internet-PC (8.8.8.8).

## Topology Diagram

![topology](images/topology.PNG)

## IP Schema Summary

### ISP Router
- g0/0 – 209.165.200.1/30  
- g0/1 – 8.8.8.1/24

### R1-HQ (NAT Router)
- g0/0 – 209.165.200.2/30 (NAT outside)
- g0/1 – 10.10.10.1/24 (Branch A)
- g0/2 – 10.20.20.1/24 (Branch B, ACL applied inbound)

### R2 / Branch A Router
- g0/0 – 192.168.10.1/24
- g0/1 – 10.10.10.2/24

### R3 / Branch B Router
- g0/0 – 192.168.20.1/24
- g0/1 – 10.20.20.2/24

### PCs
- PC-A – 192.168.10.10/24, GW 192.168.10.1
- PC-B – 192.168.20.10/24, GW 192.168.20.1
- Internet-PC – 8.8.8.8/24, GW 8.8.8.1

---

# R1-HQ Full Working Configuration
```bash
hostname R1-HQ
interface g0/0
 ip address 209.165.200.2 255.255.255.252
 ip nat outside
 no shutdown

interface g0/1
 ip address 10.10.10.1 255.255.255.0
 ip nat inside
 no shutdown

interface g0/2
 ip address 10.20.20.1 255.255.255.0
 ip nat inside
 ip access-group 100 in
 no shutdown

! NAT
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
ip nat inside source list 1 interface g0/0 overload

! ACL 100 final version
no access-list 100
access-list 100 permit ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 100 deny ip 192.168.20.0 0.0.0.255 any
access-list 100 permit ip 192.168.10.0 0.0.0.255 any
access-list 100 permit ip 10.10.10.0 0.0.0.255 any
access-list 100 permit ip 10.20.20.0 0.0.0.255 any
access-list 100 permit ip host 209.165.200.2 any

! Default Route
ip route 0.0.0.0 0.0.0.0 209.165.200.1
ip route 192.168.10.0 255.255.255.0 10.10.10.2
ip route 192.168.20.0 255.255.255.0 10.20.20.2
```

![NAT](images/nat-overlad.PNG)

![acl](images/r1aclconfig.PNG)

![r1](images/r1.config.PNG)

![defaultroute](images/r1defaultroute.PNG)
---

# R2 – Branch A Configuration
```bash
hostname R2-BranchA
interface g0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface g0/1
 ip address 10.10.10.2 255.255.255.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.10.10.1
```

![r2](images/r2config.PNG)

---

# R3 – Branch B Configuration
```bash
hostname R3-BranchB
interface g0/0
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface g0/1
 ip address 10.20.20.2 255.255.255.0
 no shutdown

ip route 0.0.0.0 0.0.0.0 10.20.20.1
```

![r3](images/r3config.PNG)

---

# ISP Router Configuration
```bash
hostname ISP
interface g0/0
 ip address 209.165.200.1 255.255.255.252
 no shutdown

interface g0/1
 ip address 8.8.8.1 255.255.255.0
 no shutdown

ip route 10.10.10.0 255.255.255.0 209.165.200.2
ip route 10.20.20.0 255.255.255.0 209.165.200.2
ip route 192.168.10.0 255.255.255.0 209.165.200.2
ip route 192.168.20.0 255.255.255.0 209.165.200.2
```

![isl](images/islconfig.PNG)

---

# Testing & Expected Results

## Branch A (PC-A)
```
ping 8.8.8.8       → SUCCESS
ping 192.168.20.10 → SUCCESS
```
---

![pca](images/pingpca.PNG)

---

## Branch B (PC-B)
```
ping 192.168.10.10 → SUCCESS
ping 8.8.8.8       → FAIL (blocked by ACL)
```
---

![pcb](images/pingpcb.PNG)

---

## Verify NAT & ACL

### NAT Verification:
```bash
show ip nat translations
show ip nat statistics
```
---
![natverification](natver.PNG)
![natstatistics](natstatistics.PNG)

---
### ACL Verification:
```bash
show access-lists
show ip interface g0/2
```
---

![aclverification](aclver.PNG)

---
 NAT translations for Branch A should appear; ACL denies for Branch B should increment.  
---


##  Troubleshooting Scenarios

| Problem | Symptom | Fix |
|----------|----------|-----|
| NAT not translating | No Internet connectivity | Verify `ip nat inside/outside` set on correct interfaces |
| ACL blocking all users | Both branches denied | Reorder ACL entries or use separate ACLs |
| Static route missing | Branches can't reach Internet | Check `ip route 0.0.0.0` settings |

---

##  Key Takeaways

- **PAT (NAT Overload)** allows many internal users to share one public IP.  
- **ACLs** define who can or cannot reach external networks.  
- Combining NAT + ACL is fundamental for secure enterprise routing.  
- This configuration mirrors real-world corporate branch access control policies.

---

# End of Lab
This is the finalized and fully validated version of the Multi-Branch NAT + ACL topology.

---

📁 Packet Tracer File: `natandacl.pkt`  
📂 Screenshot Folder: `images/`


