# Serial Cable Router Party (DCE/DTE & Static Routes Galore)
 Because why stop at two subnets when you can have a full-blown network party? Two routers, multiple devices, and enough static routes to make your head spin. If your pings fail here, it's not a glitch—it's karma for skipping subnetting practice.

![Network topology](/img/ex07.jpg)

---

## Key Concepts You’ll Flex Today

### DCE vs DTE Serial Cables: The Boss & The Intern
Serial cables are the old-school, fancy cousin of copper cross-overs, and they come with a power dynamic:
- **DCE (Data Communications Equipment)**: The *boss* of the cable pair. It dictates the **clock rate** (speed of the connection) like a micromanaging manager. *You have to manually set this on the router with the DCE cable plugged in.*
- **DTE (Data Terminal Equipment)**: The intern. It just follows the DCE’s clock rate, no questions asked.

*Pro tip: In Packet Tracer, the DCE cable will have a little clock icon on it. If you forget to set the clock rate? Your serial link will die faster than a Wi-Fi signal at a concert.*

### Static Routes: Forcing Routers to Follow Directions
Routers are stubborn—they won’t share subnet info with each other unless you explicitly tell them to. Static routes are like handing them a printed map with a highlighter: *"Go THIS way to reach the other subnet. No detours."*

---

## Network Configuration
### Subnet 1 (Switch1 Squad): 192.168.1.0/24
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC1-PC5      | -           | 192.168.1.2-6| 255.255.255.0     | 192.168.1.1     |
| Router1 (G0/0) | Gig0/0     | 192.168.1.1  | 255.255.255.0     | -               |

### Serial Link (Router Secret Tunnel): 10.10.0.0/30
*(Still the studio apartment of subnets: 2 usable IPs, zero wasted space)*
| Device       | Interface   | IP Address   | Subnet Mask       | Cable Role |
|--------------|-------------|--------------|-------------------|------------|
| Router1 (S0/0/0) | Serial0/0/0 | 10.10.0.1  | 255.255.255.252   | **DTE** (follower) |
| Router2 (S0/0/0) | Serial0/0/0 | 10.10.0.2  | 255.255.255.252   | **DCE** (boss, sets clock rate) |

### Subnet 2 (Switch2 Crew): 192.168.2.0/24
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC6-PC8 + Laptop0 | -    | 192.168.2.2-5| 255.255.255.0     | 192.168.2.1     |
| Router2 (G0/0) | Gig0/0     | 192.168.2.1  | 255.255.255.0     | -               |

---

## Implementation Steps

### 1. Cable Everything (And Don’t Mess Up The Serial Cable!)
- **Straight-Through Cables**: Connect all PCs/Laptop to their respective switches; connect switches to router Gigabit interfaces.
- **Serial Cable**: Connect Router1 ↔ Router2. *Make note of which router gets the DCE cable* (look for the clock icon in Packet Tracer—this is Router2, our boss router).

### 2. Configure Router1 (The DTE Intern)
```bash
enable
configure terminal

# Connect to Switch1's subnet
interface GigabitEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

# Connect to the serial tunnel (DTE = no clock rate needed, we just follow orders)
interface Serial0/0/0
ip address 10.10.0.1 255.255.255.252
no shutdown
exit

# CRITICAL: Tell Router1 how to reach Subnet 2 (via Router2's serial IP)
ip route 192.168.2.0 255.255.255.0 10.10.0.2

end
write memory # Save your work, or cry later when Packet Tracer crashes
```

### 3. Configure Router2 (The DCE Boss)
*Don’t forget the clock rate—this is the #1 mistake here*
```bash
enable
configure terminal

# Connect to Switch2's subnet
interface GigabitEthernet0/0
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

# Connect to the serial tunnel (DCE = set the clock rate like the boss you are)
interface Serial0/0/0
ip address 10.10.0.2 255.255.255.252
clock rate 64000 # This is non-negotiable. No clock rate = no link.
no shutdown
exit

# CRITICAL: Tell Router2 how to reach Subnet 1 (via Router1's serial IP)
ip route 192.168.1.0 255.255.255.0 10.10.0.1

end
write memory
```

### 4. Configure All PCs/Laptop
Assign IP addresses from the config tables above, and *don’t forget the default gateway* (routers will ghost your packets if you skip this step).

---

## Verify It All Works (And Celebrate)
### Test Same-Subnet Communication
From PC1:
```bash
ping 192.168.1.3 # Ping PC3 (same switch)
```
From PC8:
```bash
ping 192.168.2.2 # Ping PC6 (same switch)
```

### Test Inter-Subnet Communication
From PC1:
```bash
ping 192.168.2.5 # Ping Laptop0 (different subnet)
```
From Laptop0:
```bash
ping 192.168.1.6 # Ping PC5 (different subnet)
```

### Verify Routing Tables & Serial Link
On Router1:
```bash
show ip route
# You should see:
# C 192.168.1.0/24 (connected)
# C 10.10.0.0/30 (connected)
# S 192.168.2.0/24 [1/0] via 10.10.0.2 (your static route)

show interfaces serial0/0/0
# Check for "line protocol is up" (means the serial link is working!)
```

---

## Common Mistakes to Avoid (We’ve All Been Here)
1.  **Forgetting the clock rate on DCE**: Your serial link will say "line protocol is down" and you’ll spend 20 minutes panicking.
2.  **Typos in static routes**: Routers don’t autocorrect—if you type `192.168.3.0` instead of `192.168.2.0`, your packets will go to Narnia.
3.  **Skipping `no shutdown`**: Routers love to stay offline for attention. Always turn on your interfaces.

---

