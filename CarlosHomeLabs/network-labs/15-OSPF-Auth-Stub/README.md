# Day 25–26 — OSPF Authentication + Stub Areas (4-Router Lab)

## 🎯 Overview
In this lab you will:
- Configure **OSPF authentication** (simple & MD5)
- Configure **Stub** and **Totally Stubby** OSPF areas
- Use a **4-router enterprise topology**
- Verify OSPF neighbors, authentication, and area behaviors
- Capture screenshots for documentation

---

## 🏗️ Topology (Text Only)

```
                  Area 0 (Backbone loopback on R1)
                         [Lo0: 1.1.1.1/32]
                              R1-HQ
              (ABR for Areas 0, 10, 20, 30)
          /                 |                  \
         /                  |                   \
  (MD5 Auth, Area 0)  (Plain Auth, Area 20)  (MD5 Auth, Area 30)
       /                   |                   \
    R2-BranchA          R3-BranchB          R4-BranchC
     Area 10            Area 20 (Stub)      Area 30 (Totally Stubby)
      LAN A                LAN B                LAN C
```

---

## 📦 IP & Area Schema

### 🔹 R1-HQ (Backbone + ABR)
- Lo0: `1.1.1.1/32` — Area 0  
- g0/0: `10.0.0.1/30` — MD5 Auth → R2  
- g0/1: `10.0.0.5/30` — Simple Auth → R3  
- g0/2: `10.0.0.9/30` — MD5 Auth → R4  

### 🔹 R2 (Area 10)
- g0/0: `192.168.10.1/24`  
- g0/1: `10.0.0.2/30` → R1  

PC-A: `192.168.10.10/24`

### 🔹 R3 (Area 20 — Stub)
- g0/0: `192.168.20.1/24`  
- g0/1: `10.0.0.6/30` → R1  

PC-B: `192.168.20.10/24`

### 🔹 R4 (Area 30 — Totally Stubby)
- g0/0: `192.168.30.1/24`  
- g0/1: `10.0.0.10/30` → R1  

PC-C: `192.168.30.10/24`

---

# ⚙️ STEP 1 — Base IP + OSPF + Router IDs

## R1-HQ
```
hostname R1-HQ

interface lo0
 ip address 1.1.1.1 255.255.255.255

interface g0/0
 ip address 10.0.0.1 255.255.255.252

interface g0/1
 ip address 10.0.0.5 255.255.255.252

interface g0/2
 ip address 10.0.0.9 255.255.255.252

router ospf 1
 router-id 1.1.1.1
 network 1.1.1.1 0.0.0.0 area 0
 network 10.0.0.0 0.0.0.3 area 0
 network 10.0.0.4 0.0.0.3 area 20
 network 10.0.0.8 0.0.0.3 area 30
```

---

## R2
```
hostname R2-BranchA

interface g0/0
 ip address 192.168.10.1 255.255.255.0

interface g0/1
 ip address 10.0.0.2 255.255.255.252

router ospf 1
 router-id 2.2.2.2
 network 10.0.0.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 10
```

---

## R3
```
hostname R3-BranchB

interface g0/0
 ip address 192.168.20.1 255.255.255.0

interface g0/1
 ip address 10.0.0.6 255.255.255.252

router ospf 1
 router-id 3.3.3.3
 network 10.0.0.4 0.0.0.3 area 20
 network 192.168.20.0 0.0.0.255 area 20
```

---

## R4
```
hostname R4-BranchC

interface g0/0
 ip address 192.168.30.1 255.255.255.0

interface g0/1
 ip address 10.0.0.10 255.255.255.252

router ospf 1
 router-id 4.4.4.4
 network 10.0.0.8 0.0.0.3 area 30
 network 192.168.30.0 0.0.0.255 area 30
```

---

# 🔐 STEP 2 — OSPF Authentication

## 🔹 R1 ↔ R2 — MD5 (Area 0)
### R1
```
interface g0/0
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 R1R2-MD5
```

### R2
```
interface g0/1
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 R1R2-MD5
```

---

## 🔹 R1 ↔ R3 — Simple Authentication
### R1
```
interface g0/1
 ip ospf authentication
 ip ospf authentication-key R1R3-PLAIN
```

### R3
```
interface g0/1
 ip ospf authentication
 ip ospf authentication-key R1R3-PLAIN
```

---

## 🔹 R1 ↔ R4 — MD5 Authentication
### R1
```
interface g0/2
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 R1R4-MD5
```

### R4
```
interface g0/1
 ip ospf authentication message-digest
 ip ospf message-digest-key 1 md5 R1R4-MD5
```

---

# 🌲 STEP 3 — Stub & Totally Stubby Areas

## 🔹 Area 20 — Stub (R1 & R3)

### R1
```
router ospf 1
 area 20 stub
```

### R3
```
router ospf 1
 area 20 stub
```

---

## 🔹 Area 30 — Totally Stubby (R1 & R4)

### R1
```
router ospf 1
 area 30 stub no-summary
```

### R4
```
router ospf 1
 area 30 stub
```

---

# 🧪 STEP 4 — Verification

### Neighbor Table
```
show ip ospf neighbor
```

### Authentication Verification
```
show ip ospf interface g0/0
show ip ospf interface g0/1
show ip ospf interface g0/2
```

### Routing Table
```
show ip route ospf
```

### Stub Area Behavior
#### R3 (Stub)
```
show ip route
show ip ospf database
```

#### R4 (Totally Stubby)
```
show ip route
show ip ospf database
```

---

# 🧨 STEP 5 — End-to-End Tests

### PC-A
```
ping 192.168.20.10
ping 192.168.30.10
```

### PC-B
```
ping 192.168.10.10
ping 192.168.30.10
```

### PC-C
```
ping 192.168.10.10
ping 192.168.20.10
```

---

# 🚨 Troubleshooting

| Issue | Cause | Fix |
|------|--------|------|
| No adjacency | Auth mismatch | Ensure same type + key |
| Stuck in EXSTART | MTU mismatch | `ip mtu 1500` |
| Missing routes | Wrong area or missing network cmd | Re-check masks |
| Stub mismatch | One side stub, other not | Must match |

---

# 📁 Screenshot Structure

```
images/
  r1-ospf-interfaces.png
  r1-neighbors.png
  r3-stub-routes.png
  r4-totally-stubby-routes.png
  pc-a-tests.png
  pc-b-tests.png
  pc-c-tests.png
```

---

# 🎉 End of Lab
You now know OSPF authentication, stub and totally stubby areas — key enterprise routing skills.
