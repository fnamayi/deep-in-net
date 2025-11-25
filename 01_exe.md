
## Subnetting in Practice

Here's how I tackled Exercise 1—complete with the bugs I hit along the way.

![Network topology](img/ex01.jpg)

### The Setup

Start by dragging devices from the toolbar to match the diagram above. You'll need:
- 6 PCs (or however many your exercise requires)
- Network cables (copper crossover for PC-to-PC connections)

**Pro tip:** If devices don't power on, check the physical connections first—it's always the cables.

### Connecting Devices

1. **Select a PC** and click the connection tool
2. **Choose FastEthernet0** as the interface
3. **Select copper crossover cable** (not straight-through)
4. **Connect to the target PC's FastEthernet0**

The cable terminates with an **RJ45 connector**—a standardized connector for twisted-pair Ethernet cables. RJ45 stands for "Registered Jack-45" and is the industry standard for LAN connections.

### Configuring IP Addresses

1. Click on a PC
2. Open the **Config** tab
3. Select **FastEthernet0**
4. Navigate to **IP Configuration**
5. Set your IPv4 address and subnet mask

### Understanding Subnetting

You'll need to understand binary and decimal conversion to calculate subnets correctly. This isn't optional—it's fundamental.

**Quick reference:**
- `/24` = `255.255.255.0` = 256 addresses (254 usable)
- `/25` = `255.255.255.128` = 128 addresses per subnet
- `/30` = `255.255.255.252` = 4 addresses (2 usable, for router links)

**Cable Types**
Cable	Use Case
Copper Straight-Through	Different devices (PC to Switch)
Copper Cross-Over	Similar devices (PC to PC)

**Need a deeper dive?** Check out this [subnetting reference guide](https://www.aelius.com/njh/subnet_sheet.html).

---

**Common mistakes:**
- Using straight-through cables instead of crossover for PC-to-PC connections
- Forgetting to enable devices before testing
- Assigning IPs outside your subnet range
