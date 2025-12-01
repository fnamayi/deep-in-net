# Ex08: Three-Router Full Network Takeover (No Subnet Left Behind)
![Tri-Router Full Mesh Topology](img/ex08.jpg)

## Objective
We’re going full corporate network cosplay:
- All devices on the **same switch** can chat freely (no office cliques)
- *Every subnet* can communicate with *every other subnet* (Subnet 1 ↔ Subnet 2 ↔ Subnet 3, no left-out teams)
- Master tiny/sub-sized subnets (`/26`/`/28`) and full static routing (routers need directions to *all* subnets, not just one)

---

## Key Concepts to Flex
### Subnet Sizes: Not All /24s Are Created Equal
We’re ditching the basic `/24` for fancy, space-efficient subnets:
- **`/26` (192.168.1.0/26)**: 62 usable IPs (perfect for a medium team of PCs, no wasted addresses)
- **`/28` (192.168.3.0/28)**: 14 usable IPs (the "small team" subnet, great for a tight-knit group of PCs)
- **`/30` (serial links)**: 2 usable IPs (still the studio apartment for router gossip tunnels)

### Full Static Routing: The "Everyone Gets a Map" Rule
With 3 routers, you can’t just send one map—*every router needs directions to every single subnet*. Think of it like a office where everyone needs to know where the break room, server room, and bathroom are, not just their own desk.

### Serial DCE/DTE Recap
The boss (DCE) sets the clock rate, the intern (DTE) follows. If you forget the clock rate? Your serial link will die faster than a meeting about "synergy".

---

## Network Configuration
### Subnet 1 (Switch1 Squad: /26): 192.168.1.0/26
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC1-PC5      | -           | 192.168.1.2-6| 255.255.255.192   | 192.168.1.1     |
| Router1 (G0/0) | Gig0/0     | 192.168.1.1  | 255.255.255.192   | -               |

### Serial Link 1 (Router1 ↔ Router2): 10.10.0.0/30
| Device       | Interface   | IP Address   | Subnet Mask       | Cable Role |
|--------------|-------------|--------------|-------------------|------------|
| Router1 (S0/0/0) | Serial0/0/0 | 10.10.0.1  | 255.255.255.252   | **DTE** (intern) |
| Router2 (S0/0/0) | Serial0/0/0 | 10.10.0.2  | 255.255.255.252   | **DCE** (boss, sets clock rate) |

### Subnet 2 (Switch2 Crew: /24): 192.168.2.0/24
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC6-PC8 + Laptop1 | -    | 192.168.2.2-5| 255.255.255.0     | 192.168.2.1     |
| Router2 (G0/0) | Gig0/0     | 192.168.2.1  | 255.255.255.0     | -               |

### Serial Link 2 (Router2 ↔ Router3): 10.10.1.0/30
| Device       | Interface   | IP Address   | Subnet Mask       | Cable Role |
|--------------|-------------|--------------|-------------------|------------|
| Router2 (S0/0/1) | Serial0/0/1 | 10.10.1.1  | 255.255.255.252   | **DCE** (boss, sets clock rate) |
| Router3 (S0/0/0) | Serial0/0/0 | 10.10.1.2  | 255.255.255.252   | **DTE** (intern) |

### Subnet 3 (Switch3 Team: /28): 192.168.3.0/28
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC9-PC11     | -           | 192.168.3.2-4| 255.255.255.240   | 192.168.3.1     |
| Router3 (G0/0) | Gig0/0     | 192.168.3.1  | 255.255.255.240   | -               |

---

## Implementation Steps

### 1. Cable Everything (No Cable Chaos Allowed)
- **Straight-Through Cables**: Connect all PCs/Laptop to their switches; connect switches to router Gigabit interfaces.
- **Serial Cables**: 
  1.  Router1 ↔ Router2 (note Router2 gets DCE)
  2.  Router2 ↔ Router3 (note Router2 gets *another* DCE, it's the main boss here)

### 2. Configure Router1 (The Remote Office Router)
```bash
enable
configure terminal

# Connect to Switch1's /26 subnet
interface GigabitEthernet0/0
ip address 192.168.1.1 255.255.255.192
no shutdown
exit

# Connect to Router2's serial tunnel (DTE = no clock rate)
interface Serial0/0/0
ip address 10.10.0.1 255.255.255.252
no shutdown
exit

# CRITICAL: Routes to ALL other subnets (via Router2)
ip route 192.168.2.0 255.255.255.0 10.10.0.2
ip route 192.168.3.0 255.255.255.240 10.10.0.2

end
write memory # Save before Packet Tracer decides to crash
```

### 3. Configure Router2 (The Central Hub Boss Router)
This router is the middleman—needs routes to *all* subnets, and sets both serial clock rates:
```bash
enable
configure terminal

# Connect to Switch2's /24 subnet
interface GigabitEthernet0/0
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

# Serial link to Router1 (DCE = set clock rate)
interface Serial0/0/0
ip address 10.10.0.2 255.255.255.252
clock rate 64000
no shutdown
exit

# Serial link to Router3 (DCE = set clock rate)
interface Serial0/0/1
ip address 10.10.1.1 255.255.255.252
clock rate 64000
no shutdown
exit

# Routes to Subnet 1 and Subnet 3
ip route 192.168.1.0 255.255.255.192 10.10.0.1
ip route 192.168.3.0 255.255.255.240 10.10.1.2

end
write memory
```

### 4. Configure Router3 (The Small Team Router)
```bash
enable
configure terminal

# Connect to Switch3's /28 subnet
interface GigabitEthernet0/0
ip address 192.168.3.1 255.255.255.240
no shutdown
exit

# Connect to Router2's serial tunnel (DTE = no clock rate)
interface Serial0/0/0
ip address 10.10.1.2 255.255.255.252
no shutdown
exit

# CRITICAL: Routes to ALL other subnets (via Router2)
ip route 192.168.1.0 255.255.255.192 10.10.1.1
ip route 192.168.2.0 255.255.255.0 10.10.1.1

end
write memory
```

### 5. Configure All PCs/Laptop
Assign IP addresses + correct subnet masks (don't mix up `/26`/`/28`/`/24`!) and default gateways. Routers will ghost your packets if you use the wrong mask.

---

## Verify Everything Works (And Do a Victory Lap)
### Test Same-Subnet Communication
From PC1:
```bash
ping 192.168.1.6 # Ping PC5 (same switch)
```
From PC11:
```bash
ping 192.168.3.2 # Ping PC9 (same switch)
```

### Test Full Inter-Subnet Communication
From PC1 (Subnet1):
```bash
ping 192.168.2.5 # Ping Laptop1 (Subnet2)
ping 192.168.3.4 # Ping PC11 (Subnet3)
```
From PC11 (Subnet3):
```bash
ping 192.168.1.2 # Ping PC1 (Subnet1)
ping 192.168.2.2 # Ping PC6 (Subnet2)
```

### Verify Routing Tables
On Router1:
```bash
show ip route
# You should see:
# C 192.168.1.0/26 (connected)
# C 10.10.0.0/30 (connected)
# S 192.168.2.0/24 [1/0] via 10.10.0.2
# S 192.168.3.0/28 [1/0] via 10.10.0.2
```

---

## Common Failures (We’ve All Been Here)
1.  **Mismatched subnet masks**: If you use `/24` for a `/26` subnet, your PCs will think they're in a bigger network and refuse to talk.
2.  **Forgot a static route**: If Router1 doesn't have a route to Subnet3, PC1 will never reach PC11—double-check *all* routes on *all* routers.
3.  **No clock rate on DCE links**: Your serial link will say `line protocol is down` and you'll spend 20 minutes rechecking IPs (we've all done this).
4.  **Skipping `no shutdown`**: Routers love to stay offline for attention. Always turn on your interfaces.

