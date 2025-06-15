# STP and Redundant Path Lab (4-Switch Topology) – Cisco Packet Tracer

##  Objective

Simulate a high-availability Layer 2 network using four interconnected switches and Spanning Tree Protocol (STP). Demonstrate how STP elects a root bridge, blocks redundant paths to prevent switching loops, and automatically recovers connectivity in case of a link failure.

---

##  Real-World Scenario

A company implements a highly redundant switch network across four distribution switches to ensure continuous connectivity. STP ensures only one path is active at a time while keeping backup links in standby. This lab mirrors such enterprise scenarios where reliability is mission-critical.

---

##  Devices Used

- 4 Switches (SW1, SW2, SW3, SW4)
- 2 PCs (PC1, PC2)

---

##  Topology Diagram

```
PC1 --> SW1 <---> SW2
        |              |
        v              v
       SW3 <---> SW4 <--- PC2
```

 ![topology](images/topology.PNG)

---

##  IP Addressing

| PC   | IP Address     | Subnet Mask     |
|------|----------------|-----------------|
| PC1  | 192.168.70.10  | 255.255.255.0   |
| PC2  | 192.168.70.11  | 255.255.255.0   |

![ippc](images/pc-ip.PNG)

---

##  STP Configuration

STP (PVST+) is enabled by default.

###  Commands to Observe STP:

```bash
enable
show spanning-tree
```

- Identify the root bridge
- Locate blocking ports

![root](images/stp-root-election-blocked-port.PNG)

---

## Force SW1 to Be Root Bridge

```bash
configure terminal
spanning-tree vlan 1 priority 24576
end
```

![forcedroot](images/forced-root-bridge.PNG)

---

##  Testing Procedure

###  Before Failure:
```bash
ping 192.168.70.11
```
![pingroot](images/ping-pc1-pc2.PNG)

###  Simulate Link Failure:
- Disconnect SW1↔SW3 or SW2↔SW4

![pingroot](images/active-link-discconect.PNG)
  
- STP recalculates and unblocks another path

![pingroot](images/stp-recovery.PNG)

---

## Screenshot Summary

| File                          | Description                          |
|-------------------------------|--------------------------------------|
| ![topology](images/topology.PNG)          | Visual layout of the topology        |
| ![ippc](images/pc-ip.PNG)       | PC IP configuration                 |
| stp-root-election-blocked-port.PNG         | Root bridge output from SW1         |
| stp-root-election-blocked-port.PNG      | Blocked port on SW3                 |
| stp-root-election-blocked-port.PNG      | Blocked port on SW4                 |
| ![forcedroot](images/forced-root-bridge.PNG)        | Forcing SW1 as root bridge          |
| ![pingroot](images/ping-pc1-pc2.PNG)      | Successful ping between PCs         |
| ![pingroot](images/stp-recovery.PNG)              | STP recovering after link failure   |

---

## Key Takeaways

- STP automatically blocks redundant links to prevent loops
- A root bridge is elected based on priority + MAC
- Only one active path exists at a time; backups are ready
- STP re-converges automatically if a link fails
- This mirrors real enterprise-grade switch redundancy design

---

📁 Project File: `05-Spanning-tree-protocol.pkt`  
📂 Images Folder: `/images/`

This lab reinforces your understanding of STP behavior, link redundancy, and failure recovery — all of which are essential skills for any network admin.
