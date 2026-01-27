# DNS & DHCP Lab Handbook

**Environment:** pfSense + Windows Server (DNS/DHCP)

---

## 1. Network Topology (Baseline)

```
Internet
   │
[ Proxmox vmbr0 ]
   │
[ pfSense WAN ]  <- DHCP / upstream (must have IP)
[ pfSense LAN ]  192.168.99.214/24
   │
[ vmbr1 ]
   │
[ Windows Server ] 192.168.99.213 (DNS + DHCP)
[ Clients ] DHCP
```
---

## 2. IP Address Plan (assumptions)

| Device         | Interface | IP                   | Notes        |
| -------------- | --------- | -------------------- | ------------ |
| pfSense        | LAN       | `192.168.99.214`     | Gateway only | <- Firewall Console
| Windows Server | NIC       | `192.168.99.213`     | DNS + DHCP   |
| DHCP Pool      | —         | `192.168.99.100–200` | Clients      |
| Subnet         | —         | `255.255.255.0`      | /24          |
| Domain         | —         | `lab.local`          | Internal     |

> **Never reuse `same IP` on Windows.** 
> Two devices, one IP = packet demons.

---

## 3. Windows Server configuration

### a. Static IP

1. Server Manager -> Local Server
2. Clickt Ethernet
3. IPv4 -> Properties (Eigenschaften)
4. Set:
      - IP: `192.168.99.213/24`
      - DHCP Server: **Disabled** (Windows handles DHCP)
      - Gateway: ``192.168.99.214`` 
      > pfsense virtual console IP address
      - Preferred DNS: ``192.168.99.213`` (DNS must point to itself. Always)

---

### b. Install DNS + DHCP roles

1. Server Manager -> Add roles and features

   - Role-based installation
   - Select this server
   - Check: 
     > V DNS Server 
     > V DHCP Server
   - Accept features
   - Install

**Compleate DHCP post-installation wizard**

---

### c. DNS Reverse Lookup Zone
**Still in DNS Manager:
> Tools ->
1. Right click Reverse Lookup Zone
2. New Zone
   - Zone Type: Primary Zone
   - Zone Name: IPv4 Reverse Lookup Zone
   - Network ID: `192.168.99`
   - Dynamic Updates: Allow 

**Should now see: 99.168.192.in-addr.arpa**

---

### d. DNS Foward Lookup Zone

*DNS Manager*

1. Tools -> DNS
2. Create Foward Lookup Zone
3. Right Click Foward Lookup Zone
4. New Zone
   - Zone Type: Primary Zone
   - Zone name: lab.local
   - Allow dynamic updates: Secure & non-secure (lab mode)

## Add Host (A record)

Inside `lab.local`:
1. Right click -> New Host(A) (Neuer Host(A))
   - Name: `srv-dc01`
   - IP: ``192.168.99.214`` (host ip is the pfsense lan ip adress)
   - V Create associated PTR record 
   >this checkbox matters. it auto wires reverse lookup.

**DNS Manager -> Server Properties -> Fowarders**
1. Right-click your server in manager(WIN-85LMUKPGSH etc.)
2. Properties (Eigenschaften)
3. Fowarders (Weiterleitungen)
Add:
   - `192.168.99.214` <YOUR PFSENSE LAN IP ADDRES>
> Windows DNS resolves internal names.
> pfSense fowards external queries.

---

### e. DHCP Scope

1. Tools -> DHCP
2. Expand: IPv4 -> New Scope (Neuer Bereich)
3. Wizard opens -> Next (Weiter)

> Settings
- Name: ``Lan-Scope``
- Start IP: ``192.168.99.100``
- End IP: ``192.168.99.250``
- Mask: ``255.255.255.0``
> Bonus points
- Exclude: ``192.168.213-214`` (your own server IP and Host IP example ur pfsense LAN)
> Leaste time Default is fine.
- Lease Duration - default 8 days
> weiter
- Agree with options 
> Ja, diese Optionen jetzt konfigurieren
- New Scope Wizzard (Bereichserstellung-Assistent)
> Must input ur LAN IP Adress for standard gateway for clients ``192.168.99.214`` <- ur pfsense LAN ip
- DNS-Server configuration 
> Domain name `lab.local`. Check in window you see your servers IP adress e.g. `192.168.99.213` and google dns eg `8.8.4.4` or `8.8.8.8`.
- WIN-Server
> For short leave it empty.
---

### f. DHCP Options (this is soul)

1. Server Manager
2. Tools
3. DHCP
> a) Your server name, b) IPv4, c) <Your Scope> Right-Click

**Right click Scope, then configurate options**
> Bereichsoptionen -> Optionen konfigurieren
- 003 Router: `192.168.99.214`
- 006 DNS Server: `192.168.99.213`
- 015 DNS Domain Name: `lab.local`

**Finish wizard -> Activate Scope**
> Missconfigure and nothing works

**Clients -> Windows DNS**
**Windows DNS -> pfsense(forwarder) -> Internet**

### g. DHCP <-> DNS

1. In DHCP Console ->
2. Right click IPv4
3. Properties -> DNS
4. Enable:
   - V Dynamically update DNS records
   - V Always dynamically update
   - V Discard records when lease deleted

---

### e. Test (dont skip)

**On a client set to DHCP**
1. `ipconfig /all`
2. Check -> IPs from your scope (DNS = `192.168.99.213` domain = `lab.local`)
3. Then -> ping ``srv-dc01`` -> ``nslookup srv-dc01.lab.local`` -> ``nslookup 192.168.99.213``

**All should resolve cleanly**

---

## 4. PfSense

1. Open a bowser 
2. go to: `https://192.168.99.214` (whatever ur LAN IP is)
3. Username: ``admin`` -> Password: ``pfsense`` (default)
4. Top menu bar Service -> DHCP Server
5. LAN -> Find ``"Enable DCP server on LAn interface"`` - > <Uncheck it!>
6. Clock Save -> Apply Changes

---

## 5 Bonus

**in firewall pfsense console reset web UI settings**
0) Logout
1) Assign Interfaces
2) Set Interface(s) IP address
> option 3 `Reset webConfigurator password`

**Order matters. *Nudelholz!.***

---

[← Back to Index](../index.md)
