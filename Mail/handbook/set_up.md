# Mail-server (Weretis) + Thunderbird Setup

## 0. Before You Start
**Tip:** Ensure your Windows Server is ready.


## 1. Install DNS Role

**GUI:**
1. Server Manager -> Add Roles -> DNS Server.

2. Create **Forward Lookup Zone**: `yourdomain.local`.

3. Create **Reverse Lookup Zone**: match your network (`192.178.99.213/24`).


## 2. Open Firewall Ports
**Why:** Allow SMTP + POP3 + IMAP traffic.

**GUI Windows Defender**

1. New Rule(Neue Regeln) -> Port -> TCP -> Local Port (bestimmte lokale port) -> 25, 465, 587 -> Action (Aktion) -> Profile (Profil) -> Name SMTP
2. New Rule(Neue Regeln) -> Port -> TCP -> Local Port (bestimmte lokale port) -> 110, 995 -> Action (Aktion) -> Profile (Profil) -> Name POP3
3. New Rule(Neue Regeln) -> Port -> TCP -> Local Port (bestimmte lokale port) -> 143, 993 -> Action (Aktion) -> Profile (Profil) -> Name IMAP

---

**PowerShell:**
```powershell
New-NetFirewallRule -DisplayName "SMTP 25"  -Direction Inbound -Protocol TCP -LocalPort 25  -Action Allow
New-NetFirewallRule -DisplayName "SMTP 465" -Direction Inbound -Protocol TCP -LocalPort 465 -Action Allow
New-NetFirewallRule -DisplayName "SMTP 587" -Direction Inbound -Protocol TCP -LocalPort 587 -Action Allow
New-NetFirewallRule -DisplayName "POP3 110" -Direction Inbound -Protocol TCP -LocalPort 110 -Action Allow
New-NetFirewallRule -DisplayName "POP3 995" -Direction Inbound -Protocol TCP -LocalPort 995 -Action Allow
New-NetFirewallRule -DisplayName "IMAP 143" -Direction Inbound -Protocol TCP -LocalPort 143 -Action Allow
New-NetFirewallRule -DisplayName "IMAP 993"-Direction Inbound -Protocol TCP -LocalPort 993 -Action Allow
```

---


## 3. Install Weretis Mail-server
**Why:** Provides SMTP (send) and IMAP (receive).

1. Install Weretis (setup.exe).
2. In Weretis Admin:
   - Enable **SMTP** (25 + 465 + 587).
   - Enable **POP3** (110 + 995).
   - Enable **IMAP** (143 + 993).
3. Create Domain - E-Mail `test1@yourdomain.local` 


## 4. Thunderbird Setup

**Steps:**
1. Open Thunderbird -> Add Mail Account.
2. Enter E-Mail + Password (`test1@yourdomain.local`).


## 5. Test Mail Flow


---

[← Back to Index](../mail.md)