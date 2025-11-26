# 🖧 Network Devices: Switch vs Hub  
### Because not all network gear is created equal.

---

## 🔄 What’s the Deal with Switches and Hubs?

Ah, the eternal question: *What’s a switch? What’s a hub? And why do people call a switch a “smart hub”?*  
Well, buckle up — we’re diving in.

![Network Simulation Example](/img/ex02.jpg)  
*Yes, your simulation should look something like this. Try to match it — but remember: **no using broadcast or network IPs** as host addresses. That’s like trying to mail a letter to “Everyone” — it just doesn’t work.*

---

## 🔌 Hub: The "Shoutbox" of Networking

- **OSI Layer**: Layer 1 (Physical)
- **Nickname**: The Megaphone
- **How it works**:
  - Receives a signal on one port.
  - Screams it to **everyone else**, regardless of who it’s for.
  - Doesn’t know MAC addresses. Doesn’t care.
  - It’s basically a digital loudspeaker.

> ⚠️ **Fun fact**: Using a hub is like announcing your breakup over a school intercom. Everyone hears it — even the janitor.

---

## 🧠 Switch: The "Postal Worker" of Networking

- **OSI Layer**: Layer 2 (Data Link) — some operate at Layer 3 (Network)
- **Nickname**: The Smart Cookie
- **How it works**:
  - Learns **MAC addresses** of connected devices.
  - Builds a **MAC address table** (like a little brain).
  - Reads the destination MAC and **sends data only where it needs to go**.
  - Each port gets its own collision domain — peace at last.

> ✅ **Pro tip**: Switches don’t gossip. They deliver. Efficiently.

---

## 📊 Key Differences at a Glance

| Feature               | Hub                                  | Switch                                  |
|-----------------------|---------------------------------------|------------------------------------------|
| **OSI Layer**         | Layer 1 (Physical)                    | Layer 2 (Data Link) or Layer 3           |
| **Intelligence**      | ❌ Dumb repeater                       | ✅ Learns MAC addresses                   |
| **Data Flow**         | Broadcasts to all ports               | Forwards only to intended port           |
| **Collision Domain**  | One big chaotic mess                  | One per port — harmony!                  |
| **Bandwidth**         | Shared (like Wi-Fi at a coffee shop)  | Dedicated per port (full-duplex)         |
| **Performance**       | Slow, noisy, outdated                 | Fast, quiet, modern                      |
| **Security**          | Low — everyone sees everything        | Higher — traffic goes to the right place |
| **Cost**              | Cheap (but you get what you pay for)  | Slightly more — worth every penny        |

> 💬 *"A hub is like a megaphone. A switch is like a postal service. One causes chaos. The other delivers."*

---

## 🛠️ Configuring the Switch: Three Ways to Take Control

You’ve got options. Choose your fighter.

### 1. Console Cable (The "Old School" Way)  
That blue cable? That’s your lifeline. Here’s how to use it:

1. Add a **PT-Virtual Terminal** (or RS232 module) to your laptop.
2. **Power off** the laptop first — hot-swapping isn’t a thing here.
3. Connect the **console cable** (blue) from switch console port to laptop’s RS232.
4. Power on, open terminal.
5. Use default settings:  
   - Baud rate: 9600  
   - Data bits: 8  
   - No parity, 1 stop bit, no flow control

> 🧙‍♂️ You’re now in the CLI. Welcome to the command zone.

---

### 2. Switch GUI (The "Clicky" Way)  
Prefer point-and-click?  
- Click the switch in your simulator.
- Go to the **"Desktop"** tab.
- Use **"IP Configuration"** or **"Switch Manager"** for basic setup.

Perfect for quick tweaks — but not for real power users.

---

### 3. Terminal (CLI) – The "Real Hacker" Way  
Once you’re connected via console:

```bash
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW-Core
SW-Core(config)# exit
SW-Core# write memory
```

Boom. You’ve just leveled up.

---

## 📚 Quick Recap: What You Learned

- A **hub** is outdated, broadcasts everything, and lives at **Layer 1**.
- A **switch** is smart, efficient, and operates at **Layer 2**.
- Never assign **network** or **broadcast IPs** to devices — that’s a one-way ticket to “No Internet for You.”
- You can manage a switch via **console, GUI, or CLI** — but CLI is king.

---

> ✅ **Final Tip**: If your network feels slow and noisy, check if someone’s still using a hub. It’s 2025 — let it go.

---

*Made with caffeine and packet captures.* ☕📡