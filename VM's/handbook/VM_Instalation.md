# pfSense Firewall Configuration (Proxmox Edition)

---

## 0. Overview

**Goal:**  
To configure a pfSense firewall within a Proxmox virtualized lab environment following BSI-style security principles and best practices.

**Environment:**  
- **Platform:** Proxmox Virtual Environment (VE)
- **Firewall:** pfSense 2.7.0-RELEASE (amd64)
- **Hardware (VM):** 1 CPU, 1024 MB RAM, 20 GB Disk, 2 NICs (optional)
- **Gateway:** 10.10.10.1  (optional)
- **WAN (vtnet0):** 10.10.10.217 / 24  (optional)
- **LAN (em0):** 192.178.99.217 / 24  (optional)
- **Access:** HTTPS (WebConfigurator)  
- **DHCP:** Disabled  
- **IPv6:** Disabled

---

## 1. Proxmox Setup

**Why:**  
To host the pfSense virtual machine and provide internal (LAN) and external (WAN) bridges for network isolation.

**Steps:**  
1. Create a new VM in Proxmox:
   - Name: `pfSense-FW`, name can warry its Optional for now. Specs as well optional.
   - OS: *Other / FreeBSD*
   - Disk: 20 GB
   - CPU: 1 core
   - RAM: 1024 MB
2. Assign **two network interfaces**:
   - **WAN:** `vmbr0` (external or VLAN 215 if used for uplink)
   - **LAN:** `vmbr1` (internal bridge for lab network)
3. Boot from pfSense ISO.
4. Follow default installation:
   - Accept Auto (ZFS) installation.
   - 1. Proceed with Installation
   - 2. Stripe - No redudancy (mirror RAID system lvl 1)
   - 3. press Space to tick ok for harddrive selection
   - 4. Compleate Instalation 
   - Reboot after install.
5. Access pfSense via Proxmox console.

---

## 2. Interface Assignment

**Why:**  
To ensure that the correct NICs are mapped to WAN and LAN roles.

**Console:**  
```bash
Option 1) Assign Interfaces
Do VLANs need to be set up first? [y/n]: n
Enter the WAN interface name: vtnet0
Enter the LAN interface name: em0
Do you want to proceed? [y/n]: y
```

**Result:**  
```
WAN -> vtnet0
LAN -> em0
```

---

## 3. WAN Configuration

**Why:**  
To define the external connection that reaches the upstream gateway.

**Console:**  
```bash
Option 2) Set interface(s) IP address
Enter number of interface: 1 (WAN)
Configure IPv4 via DHCP? (y/n): n
Enter IPv4 address: 10.10.20.214 (or whatever ur server number is)
Enter subnet bit count: 24
Enter upstream gateway: 10.10.20.1 (default IPv4) || 10.10.10.1 || 10.10.10.20
Set as default gateway? (y/n): y
Configure IPv6? (y/n): n
Enable DHCP server on WAN? (y/n): n
Revert to HTTP for webConfigurator? (y/n): n
```

**Result:**  
WAN = `10.10.10.217/24`, Gateway = `10.10.10.1` or `10.10.20.1`, IPv6 disabled.

---

## 4. LAN Configuration

**Why:**  
To define the internal subnet for servers and clients in the lab.

**Console:**  
```bash
Option 2) Set interface(s) IP address
Enter number of interface: 2 (LAN)
Configure IPv4 via DHCP? (y/n): n
Enter IPv4 address: 192.168.99.217
Enter subnet bit count: 24
Gateway: [Press Enter]
> Do NOT enter 10.10.20.1 or anything else here!
Configure IPv6? (y/n): n
Enable DHCP server on LAN? (y/n): n7
Revert to HTTP for webConfigurator? (y/n): n
```

**Result:**  
LAN = `192.178.99.217/24`, DHCP and IPv6 disabled.

---

## 5. Client Windows Configuration

```bash
1. IP Address - 192.168.99.218 (or what other ip adress u use)
2. Gateway - 192.168.99.217 (gateway is ur firewall lan adress)
```

## 6. WebConfigurator Access

**Why:**  
To manage pfSense via browser with HTTPS for security.

**Steps:**  
1. From a client in the LAN, open browser -> `https://192.178.99.217/` **last octet shoud mirror VM**
2. Accept SSL certificate warning.
3. Login credentials:
   - Username: `admin`
   - Password: `pfsense`
4. (Optional) Change port for admin interface:
   - *System -> Advanced -> Admin Access -> HTTPS Port = 8443*

---

## 7. Connectivity Test

**Why:**  
To verify routing and connectivity through pfSense.

**Console:**  
```bash
Option 7) Ping host
Enter host: 8.8.8.8
```
Expected output:
```
3 packets transmitted, 3 packets received, 0% packet loss
```

---

## 8. Firewall Rules – Allowlist Model

**Why:**  
To follow a Default Deny → Allowlist principle for outbound traffic.

**Steps (GUI):**  
1. *Firewall -> Rules -> LAN*  
2. Remove “Allow All” rule.  
3. Add rules in this order:

| # | Protocol | Destination | Port | Description |
|---|-----------|--------------|------|--------------|
| 1 | ICMP | any | any | Allow Ping / Traceroute |
| 2 | TCP | any | 80 | HTTP outbound |
| 3 | TCP | any | 443 | HTTPS outbound |
| 4 | TCP/UDP | 192.178.99.147 | 53 | DNS to Domain Controller |
| 5 | UDP | 192.178.99.147 | 123 | NTP to Domain Controller |
| 6 | TCP/UDP | any | 88,389,445,464,3268,3269 | AD communication |
| 7 | Block | any | any | Default Deny |

**Apply Changes** to activate the rule set.

---

## 9. Security and BSI Alignment

| Measure | Description | Reason |
|----------|--------------|--------|
| A2/A3/A4 | Default Deny firewall approach | Minimizes attack surface |
| A6 | Admin port changed to 8443 | Avoids default 443 port scans |
| A8 | Routing disabled (firewall only) | Reduces misuse risk |
| A9 | Logging enabled | Ensures traceability |
| A17 | IPv6 disabled | Prevents unwanted traffic leaks |

**Measure - IT-Grundschutz measures — basically Germany’s official cybersecurity control catalog.**

---

## 10. Maintenance

**Backup Configuration:**  
- *Diagnostics -> Backup/Restore -> Download Configuration*

**System Updates:**  
- *Console Option 13 -> Upgrade from console*

**Reboot System:**  
- *Console Option 5 -> Reboot*

**Restart Services:**  
- *Option 16 -> Restart PHP-FPM (WebConfigurator)*

---

## 11. Verification Checklist

| Task | Command / Action | Expected Result |
|------|------------------|-----------------|
| Test WAN connectivity | `ping 8.8.8.8` | Successful replies |
| Test LAN connectivity | `ping 192.178.99.217` | Successful replies |
| Check WebGUI | Open `https://192.178.99.217` | Login page visible |
| Confirm NAT | Firewall -> NAT -> Outbound | Auto rule for 192.178.99.0/24 |
| Review Logs | Status -> System Logs -> Firewall | Normal activity only |

---

## 12. Notes for Future Improvement

- Consider migrating LAN to an RFC1918 range (e.g., 192.168.99.0/24) for production setups.
- Integrate Syslog export for centralized log collection.
- Create VLANs for separating departments (IT, Management, Operations) in larger deployments.

---

**End of Handbook – pfSense Firewall Configuration**  

---

[← Back to Index](../index.md)