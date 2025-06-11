# Troubleshooting and Failover Testing – Cisco Packet Tracer

## 🧠 Objective

Simulate a basic network and test common connectivity issues. Practice identifying and resolving misconfigurations such as wrong cables, VLAN mismatches, incorrect IPs, and powered-off interfaces.

## 🛠️ Devices Used

- 2 Switches
- 1 Router
- 3 PCs
- Ethernet cables (straight-through and crossover)

## 🔧 Lab Setup Tasks

1. Create a functioning network with:
   - 3 PCs
   - 2 switches interconnected
   - A router acting as the gateway
2. Assign static IPs:
   - PC1: 192.168.50.10
   - PC2: 192.168.50.11
   - PC3: 192.168.50.12
   - Subnet: 255.255.255.0
   - Gateway: 192.168.50.1

3. Induce common problems one by one:
   - Wrong IP address or subnet mask
   - Port administratively down
   - Wrong cable type (cross vs straight)
   - Incorrect default gateway
   - VLAN mismatch between ports

## 🧪 Troubleshooting Tasks

- Use `ipconfig`, `ping`, `tracert`, and simulation mode to identify faults
- Bring down and up interfaces on the router or switch to simulate failure
- Test recovery once the problem is corrected

## ✅ What to Demonstrate

- Clear understanding of how to isolate:
  - Layer 1 (cable)
  - Layer 2 (VLAN, port status)
  - Layer 3 (IP config, gateway)
- Fix issues and restore full communication between PCs and router

## 📷 Suggested Screenshots

- Before and after pings showing unreachable → reachable
- Port status (showing admin down/up)
- IP misconfiguration and correction
- Cable issue visual in the topology

## 🗃️ File Structure

📁 File: `troubleshooting-failover.pkt`  
📷 Screenshots: `images/`

---

This lab simulates real-world scenarios you’ll encounter in IT support and network admin roles. The ability to troubleshoot methodically is critical in any infrastructure environment.
