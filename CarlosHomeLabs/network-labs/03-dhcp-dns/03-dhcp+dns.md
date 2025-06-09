# DHCP and DNS Configuration – Cisco Packet Tracer

## Objective

Set up a DHCP server to automatically assign IP addresses to client devices and configure a DNS server to resolve domain names to IP addresses within a local network.

## Devices Used

- 1 Switch
- 1 Router
- 1 Server (DHCP + DNS)
- 3 PCs
- Copper straight-through cables

## Network Configuration

- Network: 192.168.100.0/24
- Default Gateway: 192.168.100.1 (Router)
- DHCP Range: 192.168.100.10 to 192.168.100.50
- DNS Domain: `local.lab`
- Sample hostname: `pc1.local.lab` → `192.168.100.10`

## Configuration Steps

### On the Router:
```bash
interface fa0/0
ip address 192.168.100.1 255.255.255.0
no shutdown
```

### On the Server:
1. Enable **DHCP Service**
   - Pool Name: `OfficeLAN`
   - Default Gateway: `192.168.100.1`
   - DNS Server: `192.168.100.2` (Server's own IP)
   - IP Range: `192.168.100.10 - 192.168.100.50`

2. Enable **DNS Service**
   - Add entry: `pc1.local.lab` → `192.168.100.10`
   - Add entry: `pc2.local.lab` → `192.168.100.11`

### On the PCs:
- Set to obtain IP automatically via DHCP
- Test `ping pc1.local.lab` and `ping pc2.local.lab`

## Tests Performed

- PCs received IPs automatically from DHCP : ![dhcp](dhcp-success.png)
- Router is accessible via default gateway
- DNS resolves local hostnames correctly
- DHCP lease status confirmed on server

## 📷 Topology Screenshot

![Network Topology](topology.png)

---

📁 File: `dhcp-dns.pkt`  
🖼️ Screenshot: `topology.png`
