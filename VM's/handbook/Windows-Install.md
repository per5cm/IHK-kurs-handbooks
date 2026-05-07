# Windows 11 Installation

---

## 0. Overview

**Goal:**  
To install and configure Windows 11 within a virtualized environment (Proxmox VE), following BSI-style best practices for security, system hygiene, and lab reproducibility.

**Environment:**  
- **Platform:** Proxmox Virtual Environment (VE)  
- **Operating System:** Windows 11 Pro (ISO)  
- **Hardware (VM):** 2 CPUs | 4096 MB RAM | 60 GB Disk  
- **Network:** 1 NIC (Bridged / NAT depending on lab goal)  
- **Access:** RDP (optional) / Console / SPICE  
- **DHCP:** Enabled by default from upstream device (e.g. pfSense lab router)  
- **IPv6:** Optional (disable if not used in lab environment)

---

## 0. a) Proxmox Setup

**Why:**  
To prepare a clean and isolated virtual machine environment for Windows installation.

**Steps:**  
1. Create new VM in Proxmox:  
   - Name z.b.: `Win11-Lab`  
   - OS: Microsoft Windows 11 (choose “Other OS” if ISO not listed)  
   - System: UEFI + EFI Disk enabled (required for Win 11)  
   - TPM State: Enable vTPM (Version 2.0)  
   - Disk: 60 GB (SSD emulation preferred)  
   - CPU: 2 cores (min 1 GHz each)  
   - Memory: 4 GB (minimum)  
   - Network Device: VirtIO (Bridged or NAT via `vmbr0`)  
2. Mount the Windows 11 ISO image in CD/DVD Drive.  
3. (Optional) Mount VirtIO Drivers ISO for later driver installation.  
4. Start VM and boot from ISO.

---

## 1. Windows Setup Wizard

**Why:**  
To perform a clean installation and avoid OEM bloatware or unintended configurations.

**Steps:**  
1. Choose language, time format, keyboard layout.  
2. Click **Install Now**.  
3. When prompted for product key: Skip (for evaluation) or enter valid key.  
4. Select edition: **Windows 11 Pro**.  
5. Accept license terms.  
6. Choose **Custom: Install Windows only (advanced)**.  
7. Delete all partitions -> Create new -> Next.  
8. Installation runs (~10 min).

---

## 2. Out-of-Box Experience (OOBE)

**Why:**  
To ensure privacy-friendly, local-account-first configuration.

**Steps:**  
1. Select region -> Keyboard layout.  
2. When asked for Internet connection:  
   - Disconnect NIC temporarily or press `Shift + F10`, type:  
     ```bash
     oobe\bypassnro
     ```  
   - This restarts and shows “Offline account” option.  
3. Create local administrator account.  
4. Disable Cortana, data collection, location services if not needed.  
5. Choose “Device for personal use”.

---

## 3. Driver Installation (VirtIO)

**Why:**  
To enable optimized I/O performance under Proxmox.

**Steps:**  
1. Mount VirtIO ISO in VM.  
2. Open Device Manager -> Right-click unknown devices -> “Update driver”.  
3. Browse to mounted VirtIO CD drive -> Select corresponding folders:  
   - NetKVM (for network)  
   - Balloon (for memory management)  
   - vioscsi (for storage)  
4. Verify no yellow exclamation marks remain.

---

## 4. Network Configuration

**Why:**  
To ensure proper integration with lab infrastructure (e.g. pfSense gateway).

**Steps:**  
1. Control Panel -> Network and Sharing Center -> Change Adapter Settings.  
2. Right-click adapter -> Properties -> IPv4.  
3. Assign manually (if static needed):  
   - IP: `192.178.99.150` (example)  
   - Subnet: `255.255.255.0`  
   - Gateway: `192.178.99.214` (pfSense LAN)  
   - DNS: `8.8.8.8` or internal DNS.  
4. Disable IPv6 if not required.

---

## 5. System Hardening (BSI-Aligned Basics)

| Description | Reason |
|--------------|--------|
| Rename Administrator account | Reduces target visibility |
| Enable Windows Firewall (default) | Baseline protection |
| Disable Remote Assistance & unneeded services | Reduces attack surface |
| Apply latest Windows Updates | Closes known vulnerabilities |
| Disable auto-play for removable media | Stops auto-run attacks |
| Configure BitLocker (optional) | Protects data integrity |
| Create regular restore points & backups | Forensic traceability |

---

## 6. Verification Checklist

| Task | Command / Action | Expected Result |
|------|------------------|-----------------|
| Check network status | `ping 8.8.8.8` | Replies received |
| Check hostname | `hostname` | Correct lab name |
| Check Windows activation | Settings -> System -> Activation | “Activated” or valid grace period |
| Check update status | `winver` / Windows Update | Latest build installed |
| Verify firewall | Windows Defender -> Firewall & Network Protection | Active and enabled |

---

## 7. Maintenance

**System Update:**  
- Start -> Settings -> Windows Update -> Check for updates.  

**Backup:**  
- Control Panel -> Backup and Restore -> Create system image.  

**Snapshots (Proxmox):**  
- Stop VM -> Take snapshot before major changes.  

**Restore:**  
- Select snapshot -> Rollback.

---

## 8. Notes for Future Improvement

- Integrate into Active Directory lab (once DC is ready).  
- Test RDP and VNC access under firewall rules.  
- Add Group Policy templates for automated hardening.  
- Evaluate Windows Sandbox and Hyper-V for secure testing.

---

**End of Handbook – Windows 11 Installation**

---

[← Back to Index](../index.md)