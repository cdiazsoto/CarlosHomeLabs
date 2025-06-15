# Day 8: STP and Redundant Path Lab (4-Switch Topology) – Cisco Packet Tracer

## 🧠 Objective

Simulate a high-availability Layer 2 network using four interconnected switches and Spanning Tree Protocol (STP). Demonstrate how STP elects a root bridge, blocks redundant paths to prevent switching loops, and automatically recovers connectivity in case of a link failure.

---

## 🏢 Real-World Scenario

A company implements a highly redundant switch network across four distribution switches to ensure continuous connectivity. STP ensures only one path is active at a time while keeping backup links in standby. This lab mirrors such enterprise scenarios where reliability is mission-critical.

---

## 🛠️ Devices Used

- 4 Switches (SW1, SW2, SW3, SW4)
- 2 PCs (PC1, PC2)
- Straight-through cables

---

## 🖧 Topology Diagram

```
PC1 --> SW1 <---> SW2
        |              |
        v              v
       SW3 <---> SW4 <--- PC2
```

🖼️ `images/topology-4sw-stp.png`

---

## 🔧 IP Addressing

| PC   | IP Address     | Subnet Mask     |
|------|----------------|-----------------|
| PC1  | 192.168.70.10  | 255.255.255.0   |
| PC2  | 192.168.70.11  | 255.255.255.0   |

🖼️ `images/pc1-ip.png`, `images/pc2-ip.png`

---

## ⚙️ STP Configuration

STP (PVST+) is enabled by default.

### 🔍 Commands to Observe STP:

```bash
enable
show spanning-tree
```

- Identify the root bridge
- Locate blocking ports

🖼️ `images/stp-root-election.png`  
🖼️ `images/stp-blocked-port-sw3.png`  
🖼️ `images/stp-blocked-port-sw4.png`

---

## 🏁 Optional: Force SW1 to Be Root Bridge

```bash
configure terminal
spanning-tree vlan 1 priority 24576
end
```

🖼️ `images/forced-root-bridge.png`

---

## 🧪 Testing Procedure

### ✅ Before Failure:
```bash
ping 192.168.70.11
```
🖼️ `images/ping-success-pc1-pc2.png`

### 🔄 Simulate Link Failure:
- Disconnect SW1↔SW3 or SW2↔SW4
- STP recalculates and unblocks another path

🖼️ `images/stp-recovery.png`

---

## 📷 Screenshot Summary

| File                          | Description                          |
|-------------------------------|--------------------------------------|
| topology-4sw-stp.png          | Visual layout of the topology        |
| pc1-ip.png / pc2-ip.png       | PC IP configuration                 |
| stp-root-election.png         | Root bridge output from SW1         |
| stp-blocked-port-sw3.png      | Blocked port on SW3                 |
| stp-blocked-port-sw4.png      | Blocked port on SW4                 |
| forced-root-bridge.png        | Forcing SW1 as root bridge          |
| ping-success-pc1-pc2.png      | Successful ping between PCs         |
| stp-recovery.png              | STP recovering after link failure   |

---

## ✅ Key Takeaways

- STP automatically blocks redundant links to prevent loops
- A root bridge is elected based on priority + MAC
- Only one active path exists at a time; backups are ready
- STP re-converges automatically if a link fails
- This mirrors real enterprise-grade switch redundancy design

---

📁 Project File: `stp-4switch.pkt`  
📂 Images Folder: `/images/`

This lab reinforces your understanding of STP behavior, link redundancy, and failure recovery — all of which are essential skills for any network admin.
