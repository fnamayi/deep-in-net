well well well its you again. for this i decided to consult ai to help you since the last one you complained lots

# 🌐 **Cisco Packet Tracer Lab: ex03 - Server Services & DNS Configuration**

> *"A network without servers is like a city without power. Let’s build one that actually works."*

---

## 🏗️ **Lab Overview: ex03**

In this lab, you'll build a functional local network in **Cisco Packet Tracer** where:
- Servers use **static IPs**
- Each server does **one job only** (no multitasking!)
- PCs get IPs via **DHCP**
- The **HTTPS server** shows a "Hello" message — and **HTTP is disabled**
- A **user account** `deepinnet` has access to the FTP server
- **DNS** maps `deep-in-net.com` to `deep-in-net.local`
- And yes — `https://deep-in-net.com` actually works ✅

Let’s get building.

---

## 🔧 **Network Design (ex03)**

### Devices Used:
- 1 **Server-PT** (x3: for DNS, DHCP, HTTPS/FTP)
- 1 **Switch** (2960)
- Multiple **PC-PT** clients
- (Optional) Router if connecting beyond LAN

### IP Scheme:
- Network: `192.168.1.0 /24`
- Subnet Mask: `255.255.255.0`
- Gateway: `192.168.1.1` (if using router)

| Server | Purpose | Static IP |
|--------|--------|----------|
| `SRV-DNS` | DNS Server | `192.168.1.10` |
| `SRV-DHCP` | DHCP Server | `192.168.1.11` |
| `SRV-WEBFTP` | HTTPS + FTP Server | `192.168.1.99` |

> ✅ **Rule**: One service per server. No cheating.

---

## 🛠️ **Configuration Steps**

---

### ✅ **ex03-dns**: Configure DNS Records

On **SRV-DNS (192.168.1.10)** → **Services** → **DNS**

#### Add Records:
| Name | Type | Value |
|------|------|-------|
| `deep-in-net.local` | `A` | `192.168.1.99` |
| `deep-in-net.com` | `CNAME` | `deep-in-net.local` |

> ⚠️ CNAME at root? Not ideal in real life — but **Packet Tracer allows it for learning**.

✅ Now, `deep-in-net.com` points to the HTTPS server via `deep-in-net.local`.

---

### ✅ **ex03-dhcp**: Configure DHCP Server

On **SRV-DHCP (192.168.1.11)** → **Services** → **DHCP**

#### DHCP Pool Settings:
- **Pool Name**: `LAN-POOL`
- **Default Gateway**: `192.168.1.1`
- **DNS Server**: `192.168.1.10` ← Your DNS server
- **Start IP**: `192.168.1.100`
- **Max Users**: `50`
- **Subnet Mask**: `255.255.255.0`

> 🔄 All **PCs must use DHCP** (no static IPs for clients).

---

### ✅ **ex03-https**: HTTPS Server with "Hello" Message

On **SRV-WEBFTP (192.168.1.99)** → **Services** → **HTTP**

#### HTTPS Settings:
- ✅ Enable **HTTPS**
- ❌ Disable **HTTP** (uncheck it)
- Edit `index.html` to display:
  ```html
  <h1>Hello from deep-in-net!</h1>
  <p>HTTPS is secure. HTTP is not invited.</p>
  ```

> 🔐 HTTPS uses a built-in self-signed cert in Packet Tracer — no extra setup needed.

---

### ✅ **ex03-ftp**: Create User & Enable FTP

Still on **SRV-WEBFTP** → **Services** → **FTP**

#### Create User:
- Click **"Users"** → **"Add"**
- **Username**: `deepinnet`
- **Password**: `password123` (or any)
- **Access Level**: `RWDNL`
  - ✔️ Read
  - ✔️ Write
  - ✔️ Delete
  - ✔️ Navigate
  - ✔️ List

✅ User `deepinnet` can now log in and manage files.

> 📁 Test with a PC: Open **Web Browser** → `ftp://192.168.1.99` → Log in with credentials.

---

## 🌍 **Final Test: Does It Work?**

On any **PC client**:

1. **Command Prompt** → `ipconfig`  
   → Confirm IP is in `192.168.1.100+` range (from DHCP)

2. `nslookup deep-in-net.com`  
   → Should resolve to `192.168.1.99`

3. Open **Web Browser** → Go to:
   ```
   https://deep-in-net.com
   ```
   → ✅ Should show "Hello from deep-in-net!"  
   → ❌ If HTTP works, you failed — disable it!

4. Try FTP:
   ```
   ftp://192.168.1.99
   ```
   → Log in as `deepinnet` → Upload/delete a file? Success!

---

## 🧠 **Knowledge Check: Key Concepts**

### 1. **What is a Server?**
A **server** is a computer or device that provides services (like web, DNS, FTP) to other devices (clients) over a network.

> 🖥️ In this lab: DNS, DHCP, HTTPS, FTP — each has its own dedicated server.

---

### 2. **DHCP: Dynamic Host Configuration Protocol**
- **Purpose**: Automatically assigns IP addresses to devices.
- **How it works**: Uses **DORA process**:
  1. **D**iscover
  2. **O**ffer
  3. **R**equest
  4. **A**cknowledge
- **Port**: UDP 67 (server), UDP 68 (client)
- **OSI Layer**: Application (Layer 7), relies on Transport (Layer 4 - UDP)

---

### 3. **DNS: Domain Name System**
- **Purpose**: Translates domain names (`deep-in-net.com`) to IP addresses.
- **Role**: The "phonebook" of the internet.
- **Common Records**:
  - `A`: Name → IP
  - `CNAME`: Alias → Name
  - `MX`: Mail server
  - `NS`: Name server
- **Port**: UDP 53
- **OSI Layer**: Application (Layer 7)

---

### 4. **HTTP vs HTTPS**
| Feature | HTTP | HTTPS |
|--------|------|--------|
| Security | ❌ Unencrypted | ✅ Encrypted (SSL/TLS) |
| Port | 80 | 443 |
| Data Risk | High | Low |
| OSI Layer | Application (Layer 7) | Application (Layer 7) |

> 🔐 HTTPS protects against eavesdropping and tampering.

---

### 5. **FTP: File Transfer Protocol**
- **Purpose**: Transfer files between client and server.
- **Ports**: 
  - `21` (control/command)
  - `20` (data) — or dynamic in passive mode
- **OSI Layer**: Application (Layer 7)
- **Authentication**: Requires username/password

---

### 6. **TCP vs UDP**
| Feature | TCP | UDP |
|--------|-----|-----|
| Connection | ✅ Connection-oriented | ❌ Connectionless |
| Reliability | High (acknowledgments) | Low (best-effort) |
| Speed | Slower | Faster |
| Use Cases | Web, Email, FTP | Video, DNS, VoIP |
| **OSI Layer** | **Transport (Layer 4)** | **Transport (Layer 4)** |

---

### 7. **What is a Port?**
- A **port** is a virtual endpoint for communication.
- Identifies specific processes or services (e.g., 443 = HTTPS).
- Works with IP to route data to the right app.

---

### 8. **Protocol Ports & OSI Layers Summary**

| Protocol | Port | OSI Layer |
|---------|------|-----------|
| HTTP | 80 | Application (7) |
| HTTPS | 443 | Application (7) |
| FTP | 20/21 | Application (7) |
| DNS | 53 (UDP/TCP) | Application (7) |
| DHCP | 67/68 | Application (7) |
| TCP | N/A | Transport (4) |
| UDP | N/A | Transport (4) |

---

## 🎯 **Lab Completion Checklist**

| Task | Done? ✅ |
|------|--------|
| Servers use static IPs | ☐ |
| DHCP assigns IPs to PCs | ☐ |
| HTTP is disabled on web server | ☐ |
| HTTPS shows "Hello" message | ☐ |
| DNS: `deep-in-net.local` → `192.168.1.99` | ☐ |
| DNS: `deep-in-net.com` → CNAME → `deep-in-net.local` | ☐ |
| `https://deep-in-net.com` works | ☐ |
| FTP user `deepinnet` with RWDNL access | ☐ |
| All services separated by server | ☐ |

---

> 💬 *"You don't have to be a network god to do this — just pay attention to detail."*

---
well you see how info i had to put out and i have ommited some just to make you go look for it mwa ha haaa

**Lab Version**: 1.0  
**Created with**: Cisco Packet Tracer 🚦  
**Author**: namayi hehe AI