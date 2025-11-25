# Installing Cisco Packet Tracer

A network simulation tool for experimenting with virtual computer networks. Because breaking real networks is frowned upon, and somehow "I was learning" doesn't satisfy the IT department.

This guide covers installation for DevOps and cloud networking concepts.

---

## Prerequisites

- Cisco Networking Academy account (free)
- Ubuntu-based Linux distribution
- Terminal access
- Willingness to accept license agreements you won't read

---

## Download

1. Create an account at [Cisco Networking Academy](https://www.netacad.com/about-networking-academy/packet-tracer/)
2. Download the appropriate package for your system

---

## Installation (Ubuntu)

The downloaded file will have a `.deb` extension.

### Step 1: Install the Package

Navigate to your download directory and run:

```bash
sudo dpkg -i CiscoPacketTracer_900_Ubuntu_64bit.deb
```

### Step 2: Resolve Dependencies

Dependency issues are practically guaranteed. Fix them with:

```bash
sudo apt --fix-broken install
```

---

## Launch

1. Open Cisco Packet Tracer from your applications menu
2. Accept the License Agreement
3. Sign in with your Cisco Networking Academy credentials

---

## Additional Resources

For a visual walkthrough, refer to the [GeeksforGeeks Installation Guide](https://www.geeksforgeeks.org/installation-guide/how-to-install-cisco-packet-tracer-on-linux/).

---

*Now go simulate some networks before touching production.*