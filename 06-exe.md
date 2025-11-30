#  Dual-Router Inter-Subnet Shenanigans

## Objective

![ Topology](img/ex06.jpg)

Connect two PCs across two routers, because one router was clearly not enough to flex our networking skills (or because we wanted to watch packets take the scenic route between subnets).

What is a **Routing Table?**

A routing table is a data structure stored in a router (or any device that performs routing, like a computer or firewall) that acts as a "map" for directing network traffic. It contains a list of routes—essentially instructions on how to forward packets from one network to another based on their destination IP addresses.


**Requirements:**
- PC1 (Subnet 1) ↔ PC2 (Subnet 2) communication
- No lost packets, no excuses (routers don’t care about your "bad day")

---

## Key Concept: Routing Tables
Think of this as the router’s personal GPS cheat sheet—no pizza stains, no wrong turns, just cold, hard directions for your packets.

**What it does:**
1.  Maps destination subnets to their "next hop" router/interface
2.  Stops routers from guessing where to send traffic (unlike you trying to find a gas station at 2am)
3.  Lets you manually dictate paths (static routes) or let routers gossip to find paths (dynamic routing)

**View the magic:**
```bash
show ip route
```
*(Spoiler: The `C` means "directly connected" (router knows this place), `S` means "static" (you told it exactly where to go))*

---

## Network Configuration

### Subnet 1 (PC1’s Neighborhood): 192.168.1.0/24
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| PC1          | -           | 192.168.1.2  | 255.255.255.0     | 192.168.1.1     |
| Router1 (G0/0) | Gig0/0     | 192.168.1.1  | 255.255.255.0     | -               |

### Router Link (The Secret Tunnel): 10.10.0.0/30
This is the studio apartment of subnets: only 2 usable IPs, zero wasted space, and no room for uninvited devices. Perfect for router gossip.
| Device       | Interface   | IP Address   | Subnet Mask       |
|--------------|-------------|--------------|-------------------|
| Router1 (G0/1) | Gig0/1     | 10.10.0.1    | 255.255.255.252   |
| Router2 (G0/0) | Gig0/0     | 10.10.0.2    | 255.255.255.252   |

### Subnet 2 (PC2’s Neighborhood): 192.168.2.0/24
| Device       | Interface   | IP Address   | Subnet Mask       | Default Gateway |
|--------------|-------------|--------------|-------------------|-----------------|
| Router2 (G0/1) | Gig0/1     | 192.168.2.1  | 255.255.255.0     | -               |
| PC2          | -           | 192.168.2.2  | 255.255.255.0     | 192.168.2.1     |

---

## Implementation Steps

### 1. Assemble the Gear
Drag 2 PCs, 2 Routers, and grab the right cables:
- **Straight-Through:** PC ↔ Router (different devices, no drama)
- **Cross-Over:** Router ↔ Router (same device type, they need a direct line to gossip)

### 2. Configure Router1 (CLI)
*(Pro tip: If you get stuck, remember `enable` is your gateway out of "I can’t do anything" mode)*
```bash
enable
configure terminal

# Connect to PC1's subnet
interface GigabitEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown  # Don't forget this—routers are dramatic and stay off unless told otherwise
exit

# Connect to Router2's secret tunnel
interface GigabitEthernet0/1
ip address 10.10.0.1 255.255.255.252
no shutdown
exit

# Tell Router1 how to find Subnet 2 (via Router2)
ip route 192.168.2.0 255.255.255.0 10.10.0.2

end
write memory  # Save your work, unlike that college paper you forgot to backup
```

### 3. Configure Router2 (CLI)
```bash
enable
configure terminal

# Connect to the secret tunnel
interface GigabitEthernet0/0
ip address 10.10.0.2 255.255.255.252
no shutdown
exit

# Connect to PC2's subnet
interface GigabitEthernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

# Tell Router2 how to find Subnet 1 (via Router1)
ip route 192.168.1.0 255.255.255.0 10.10.0.1

end
write memory
```

### 4. Configure PCs
Plug in the IPs and gateways from the config tables above. No typos—routers will reject your packets out of spite if you misspell an IP.

---

## Verify Connectivity
### Test the Link
From PC1:
```bash
ping 192.168.2.2
```
From PC2:
```bash
ping 192.168.1.2
```
If it works? Do a little victory dance. If not? Check your static routes first—routers are terrible at reading between the lines, they need explicit directions like a tourist in a foreign country.

### Check Routing Tables
On Router1:
```bash
show ip route
```
You should see:
- `C 192.168.1.0/24` (directly connected, Router1 lives here)
- `C 10.10.0.0/30` (directly connected, the secret tunnel)
- `S 192.168.2.0/24` (static route, you told it exactly where to go)

---

## Knowledge Check (No Cheating… Unless You’re a Router)
- [ ] What is a routing table, and why is it the router’s favorite accessory?
- [ ] Explain how static routes make routers follow your orders (instead of doing their own thing)
- [ ] Why did we use a /30 subnet for the router link? (Hint: It’s the smallest usable subnet for point-to-point connections)