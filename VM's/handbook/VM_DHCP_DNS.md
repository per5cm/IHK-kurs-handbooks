# DNS & DHCP Lab Handbook

**Environment:** pfSense + Windows Server (DNS/DHCP)

---

## 1. Network Topology (Baseline)

```
Internet
   │
[ Proxmox vmbr0 ]
   │
[ pfSense WAN ]  ← DHCP / upstream (must have IP)
[ pfSense LAN ]  192.168.99.213/24
   │
[ vmbr1 ]
   │
[ Windows Server ] 192.168.99.214 (DNS + DHCP)
[ Clients ] DHCP
```
---

## 2. IP Address Plan (assumptions)

| Device         | Interface | IP                   | Notes        |
| -------------- | --------- | -------------------- | ------------ |
| pfSense        | LAN       | `192.168.99.213`     | Gateway only | <- Firewall Console
| Windows Server | NIC       | `192.168.99.214`     | DNS + DHCP   |
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
      - IP: `192.168.99.214/24`
      - DHCP Server: **Disabled** 
      > Windows handles DHCP
      - Gateway: 192.168.99.213 
      > pfsense virtual console IP address
      - Preferred DNS: 192.168.99.214 
      > DNS must point to itself. Always

---

### b. Install DNS + DHCP roles
> Server Manager -> Add roles and features
  * Role-based installation
  * Select this server
  * Check: x DNS Server x DHCP Server
  > Accept features
    > Install

**Compleate DHCP post-installation wizard**

---

### c. DNS Reverse Lookup Zone
**Still in DNS Manager:
> Right click Reverse Lookup Zone
  > New Zone
    > Primary Zone
      > IPv4 Reverse Lookup Zone
        > Network ID: `192.168.99`
          > Allow dynamic updates 

**Should now see: 99.168.192.in-addr.arpa**

---

### d. DNS Foward Lookup Zone

> Tools -> DNS
**Create Foward Lookup Zone**
  > Right Click Foward Lookup Zone
    > New Zone
      > Primary Zone
        > Zone name: lab.local
          > Allow dynamic updates: Secure & non-secure (lab mode)

# Add Host (A record)
> Inside ``lab.local``:
  > Right click -> New Host(A)
    > Name: srv-dc01
      > IP: 192.168.99.213
        > x Create associated PTR record (this checkbox matters. it auto wires reverse lookup.)

---

### e. DHCP Scope
> Tools -> DHCP
  > Expand: IPv4 -> New Scope (Neuer Bereich)
    > Wizard opens -> Next (Weiter)
**Settings**
- Name: Lan-Scope
- Start IP: 192.168.99.100
- End IP: 192.168.99.200
- Mask: 255.255.255.0
**Bonus points**
- Exclude: 192.168.99.1-99
- Exclude: 192.168.213-214
**Leaste time**
- Default is fine.
       > Lease Duration - default 8 days (weiter)
         > Agree with options (Ja, diese Optionen jetzt konfigurieren)
---

### f. DHCP Options (this is soul)
**Right click Scope, then configurate options (Bereichsoptionen -> Optionen konfigurieren)**
> 003 Router: `192.168.99.213`
> 006 DNS Server: `192.168.99.214`
> 015 DNS Domain Name: `lab.local`
**Finish wizard -> Activate Scope**

**Clients -> Windows DNS**
**Windows DNS -> pfsense(forwarder) -> Internet**

### g. DHCP <-> DNS (important)
> In DHCP Console ->
  > Right click IPv4
    > Properties -> DNS
      > Enable:
       - x Dynamically update DNS records
       - x Always dynamically update
       - x Discard records when lease deleted

---

### e. Test (dont skip)

**On a client set to DHCP**
> `ipconfig /all`
  > Check -> IPs from your scope (DNS = `192.168.99.213` domain = `lab.local`)
    > Then -> ping ``srv-dc01`` -> ``nslookup srv-dc01.lab.local`` -> ``nslookup 192.168.99.213``

**All should resolve cleanly**

---

## 4. PfSense
> Open a bowser 
  > go to: `https://192.168.99.213` (whatever ur LAN IP is)
    > Username: admin -> Password: pfsense (default)
      > Top menu bar Service -> DHCP Server
        > LAN -> Find "Enable DCP server on LAn interface" - > Uncheck it!
          > Clock Save -> Apply Changes

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
