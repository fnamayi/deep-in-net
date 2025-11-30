# Router Configuration

## Objective

![Network Topology](img/ex04.jpg)

Connect two PCs through a router and establish communication between different networks. Few devices, few configurations. Famous last words.

---

## Key Concepts

### What is a Router?
A router is a networking device that forwards data packets between computer networks. It operates at **OSI Layer 3 (Network Layer)** and makes decisions based on IP addresses.

**Role:** Connects different networks and determines the best path for data to travel. Think of it as a traffic controller that actually does its job.

### Router vs Switch

| Feature | Switch | Router |
|---------|--------|--------|
| OSI Layer | Layer 2 (Data Link) | Layer 3 (Network) |
| Addressing | MAC addresses | IP addresses |
| Function | Connects devices in same network | Connects different networks |
| Broadcast Domain | Single | Separates |
| Intelligence | Smart | Smarter |

### Default Gateway
The IP address of the router interface that devices use to communicate with other networks. It's the "exit door" for traffic leaving the local network. Without it, your packets go nowhere and contemplate their existence.

---

## Network Configuration

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|--------|-----------|------------|-------------|-----------------|
| PC0 | - | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| Router | Gig0/0 | 192.168.1.1 | 255.255.255.0 | - |
| Router | Gig0/1 | 192.168.2.1 | 255.255.255.0 | - |
| PC1 | - | 192.168.2.2 | 255.255.255.0 | 192.168.2.1 |

> **Note:** Pay attention to cable types. Details matter. The network doesn't care about your intentions.

---

## Implementation Steps

### 1. Place Devices
- Drag 2 PCs and 1 Router from the device panel
- Arrange according to topology diagram

### 2. Connect Devices
- Use **Copper Straight-Through** cables (PC to Router = different devices)
- PC0 → Router Gig0/0
- PC1 → Router Gig0/1

### 3. Configure Router (CLI)

Access the router CLI and enter the following:

```bash
enable
configure terminal

# Configure first interface (connects to PC0)
interface GigabitEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

# Configure second interface (connects to PC1)
interface GigabitEthernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

end
write memory
```

> **Pro tip:** `no shutdown` turns on the interface. Forgetting this command is a rite of passage.

### 4. Configure PCs

**PC0:**
- IP Address: 192.168.1.2
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1

**PC1:**
- IP Address: 192.168.2.2
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.2.1

### 5. Verify Connectivity

From PC0:
```
ping 192.168.2.2
```

From PC1:
```
ping 192.168.1.2
```

---

## Verification Commands

```bash
# View interface status
show ip interface brief

# View routing table
show ip route

# View running configuration
show running-config
```

---



*When the sun sets, we dive deeper. Until then, master the basics.*