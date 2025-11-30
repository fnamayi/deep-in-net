# Inter-Subnet Communication

## Objective

![Network Topology](img/ex05.jpg)

Connect multiple PCs across two subnets through switches and a router. Because one subnet was too easy.

---

## Requirements

- All devices on the same switch can communicate with each other
- Subnet 1 ↔ Subnet 2 communication enabled
- Router facilitates inter-subnet traffic

---

## Network Configuration

### Subnet 1: 192.168.1.0/24

| Device | IP Address | Subnet Mask | Default Gateway |
|--------|------------|-------------|-----------------|
| PC0 | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| PC1 | 192.168.1.3 | 255.255.255.0 | 192.168.1.1 |
| PC2 | 192.168.1.4 | 255.255.255.0 | 192.168.1.1 |
| Router (Gig0/0) | 192.168.1.1 | 255.255.255.0 | - |

### Subnet 2: 192.168.2.0/24

| Device | IP Address | Subnet Mask | Default Gateway |
|--------|------------|-------------|-----------------|
| PC3 | 192.168.2.2 | 255.255.255.0 | 192.168.2.1 |
| PC4 | 192.168.2.3 | 255.255.255.0 | 192.168.2.1 |
| PC5 | 192.168.2.4 | 255.255.255.0 | 192.168.2.1 |
| Router (Gig0/1) | 192.168.2.1 | 255.255.255.0 | - |

---

## Implementation Steps

### 1. Place Devices
- 6 PCs, 2 Switches, 1 Router
- Arrange per topology diagram

### 2. Connect Devices
- **Copper Straight-Through:** PC to Switch, Switch to Router
- PC0, PC1, PC2 → Switch1 → Router Gig0/0
- PC3, PC4, PC5 → Switch2 → Router Gig0/1

### 3. Configure Router (CLI)

```bash
enable
configure terminal

interface GigabitEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

interface GigabitEthernet0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit

end
write memory
```

### 4. Configure PCs
Assign IP addresses and gateways per the tables above.

---

## Verify Connectivity

```bash
# From PC0 - same subnet
ping 192.168.1.3
ping 192.168.1.4

# From PC0 - different subnet
ping 192.168.2.2
ping 192.168.2.3
ping 192.168.2.4
```

*If all pings succeed, congratulations. If not, check your gateways. It's always the gateways.*