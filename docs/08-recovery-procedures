# Recovery Procedures

## Overview

This document covers recovery procedures for the most likely failure scenarios in the lab — firewall lockout, VM loss, switch misconfiguration, and full environment rebuild. The goal is to make every failure mode recoverable without a full rebuild, and to document the recovery path before it is needed.

---

## Credential Storage

All lab device credentials (OPNsense, switches, VMs) are stored in a **7-Zip AES-encrypted archive** on the Alienware management workstation. This archive must be accessible before attempting any recovery procedure that requires authentication.

**If the Alienware is unavailable:** Ensure the encrypted archive is also copied to the external WD drive as part of the backup procedure below.

---

## Backup Procedures

### Backup Storage — WD Discovery External Drive (2TB)

The WD Discovery external drive is the primary offline backup destination for all critical lab artifacts. It connects to the Alienware workstation via USB and is connected manually when performing backups — it is not permanently attached.

**Target folder structure on WD drive:**

```
WD Drive/
├── backups/
│   ├── opnsense/
│   │   └── opnsense-backup-YYYY-MM-DD.xml
│   ├── credentials/
│   │   └── lab-credentials.7z
│   └── vms/
│       ├── kali-baseline-YYYY-MM-DD.utm
│       ├── windows11-baseline-YYYY-MM-DD.utm
│       ├── metasploitable2-baseline-YYYY-MM-DD.utm
│       └── ubuntu-server-baseline-YYYY-MM-DD.utm
└── windows-backup/
    └── (existing Windows machine backup)
```

**Current backup status:**

| Item | Primary Location | WD Drive | Status |
|------|-----------------|----------|--------|
| OPNsense XML | Mac Server | ⚠️ Not yet copied | Action required |
| Credentials archive (7-Zip) | Alienware | ⚠️ Not yet copied | Action required |
| Kali Linux VM export | UTM (snapshot only) | ⚠️ Not yet exported | Action required |
| Windows 11 VM export | UTM (snapshot only) | ⚠️ Not yet exported | Action required |
| Metasploitable 2 VM export | UTM (snapshot only) | ⚠️ Not yet exported | Action required |
| Ubuntu Server VM export | UTM (snapshot only) | ⚠️ Not yet exported | Action required |
| Windows machine backup | WD Drive | ✅ Exists | Current |

> **Priority action:** Complete all items marked "Action required" before conducting any destructive lab exercises. The OPNsense XML and credentials archive are the highest priority — both are small files that take under a minute to copy.

---

### OPNsense Configuration Backup

OPNsense configuration is exported as an XML file containing all firewall rules, interfaces, VLAN assignments, and settings. Backups are taken manually — triggered by any significant change to firewall rules, interface configuration, or before any lab upgrade or training exercise.

**Export procedure:**
1. Log into OPNsense GUI → **System → Configuration → Backups**
2. Click **Download configuration**
3. Save using the naming convention: `opnsense-backup-YYYY-MM-DD.xml`
4. Save to both locations:
   - **Primary:** Mac Server host machine
   - **Secondary:** `backups/opnsense/` on WD Discovery drive

**Backup triggers — take a backup before or after:**
- Any firewall rule addition, modification, or deletion
- Any interface or VLAN configuration change
- Any OPNsense version upgrade
- Any lab training exercise that modifies firewall policy
- Any recovery procedure that results in a working configuration

---

### Credentials Archive Backup

The 7-Zip AES-encrypted credentials archive on the Alienware should be copied to `backups/credentials/` on the WD drive whenever credentials are added or changed. The archive remains encrypted — do not extract or store credentials unencrypted anywhere on the drive.

---

### VM Snapshot Management

All four lab VMs have a baseline snapshot configured in UTM representing a clean, fully configured, working state. UTM snapshots are the fastest recovery mechanism — they live on the Mac Server and can restore a VM in minutes. However, snapshots are not a substitute for offline VM exports: if the Mac Server's internal drive fails, all snapshots are lost along with the VMs.

**Two-layer VM backup strategy:**

| Layer | Mechanism | Location | Protects Against |
|-------|-----------|----------|-----------------|
| 1 — Fast recovery | UTM snapshot | Mac Server internal drive | VM corruption, bad exercise state |
| 2 — Offline backup | UTM VM export (.utm file) | WD Discovery drive | Mac Server drive failure, accidental deletion |

**Snapshot inventory:**

| VM | UTM Snapshot | WD Export | Baseline State |
|----|-------------|-----------|----------------|
| Ubuntu Server (192.168.10.4) | ✅ Exists | ⚠️ Pending | Configured, services running |
| Kali Linux (192.168.20.20) | ✅ Exists | ⚠️ Pending | Configured, tooling installed |
| Windows 11 (192.168.30.10) | ✅ Exists | ⚠️ Pending | Configured, working state |
| Metasploitable 2 (192.168.30.20) | ✅ Exists | ⚠️ Pending | Configured, working state |

**Estimated VM export sizes:**

| VM | Estimated Size |
|----|---------------|
| Kali Linux | 20–40 GB |
| Windows 11 | 30–60 GB |
| Metasploitable 2 | 3–5 GB |
| Ubuntu Server | 5–10 GB |
| **Total** | **~60–115 GB** |

Total backup footprint is well within the 2TB drive capacity.

**UTM VM export procedure:**
1. Shut down the VM in UTM
2. Right-click the VM in UTM → **Export**
3. Save to `backups/vms/` on the WD drive using the naming convention: `vmname-baseline-YYYY-MM-DD.utm`
4. Verify the export file size is reasonable (not suspiciously small)

**Snapshot restore procedure (UTM):**
1. Open UTM on the Mac Server
2. Right-click the affected VM → **Manage Snapshots**
3. Select the baseline snapshot → **Restore**
4. Start the VM and verify network connectivity and services

**Snapshot best practice:** Before any destructive lab exercise (exploit development, malware analysis, destructive testing), verify the snapshot exists and is current. After any exercise that intentionally modifies a VM's baseline configuration, take an updated snapshot and note the change.

---

## Recovery Scenario 1: Firewall Management Lockout

**Scenario:** A misconfigured firewall rule blocks access to the OPNsense GUI from VLAN 10, making normal management access unavailable.

**This is the highest-priority recovery scenario** — it can result from a single incorrect rule and affects all other lab operations.

### Recovery Path A — Emergency VLAN 99 (Primary)

1. Retrieve the Alienware workstation or any available laptop
2. Configure a static IP on the laptop's Ethernet adapter:
   - IP Address: `192.168.99.50`
   - Subnet Mask: `255.255.255.0`
   - Gateway: `192.168.99.1`
   - DNS: `192.168.99.1`
3. Connect the laptop **directly to the Emergency VLAN 99 port** on the OPNsense firewall via Ethernet — this is a direct physical connection to the firewall, bypassing the Netgear switch entirely
4. Open a browser and navigate to `https://192.168.99.1`
5. Log in using credentials from the encrypted 7-Zip archive on the Alienware
6. Navigate to **Firewall → Rules → VLAN_10_Management** and identify the misconfigured rule
7. Correct or remove the rule
8. Verify VLAN 10 management access is restored from a normal management device
9. Disconnect the emergency laptop and return its Ethernet adapter to DHCP

### Recovery Path B — OPNsense Console (Last Resort)

Use only if VLAN 99 access fails.

**Hardware available:** OPNsense firewall device has HDMI output and USB ports. Monitor and keyboard can be directly connected.

1. Connect a monitor via HDMI and a USB keyboard directly to the OPNsense firewall device
2. At the OPNsense console menu:
   - **Option 3** — Reset webConfigurator password (if credential issue)
   - **Option 8** — Shell access (for manual rule inspection or editing)
3. If a full configuration reset is required:
   - **Option 4** — Reset to factory defaults (this erases all configuration)
   - Immediately restore from the most recent XML backup (see restore procedure below)

### OPNsense Configuration Restore

1. Log into OPNsense GUI → **System → Configuration → Backups**
2. Under **Restore**, click **Choose file**
3. Select the most recent `opnsense-backup-YYYY-MM-DD.xml` from the Mac Server or WD drive
4. Click **Restore configuration** — OPNsense will apply settings and may reboot
5. Verify all VLAN interfaces are active: **Interfaces → Overview**
6. Verify firewall rules: **Firewall → Rules** — check each VLAN interface
7. Run the post-rebuild verification checklist at the end of this document

---

## Recovery Scenario 2: VM Loss or Corruption

**Scenario:** A VM becomes corrupted, behaves unexpectedly, or needs to be rolled back after a destructive exercise.

### Primary Recovery — UTM Snapshot Restore

1. Open UTM on the Mac Server
2. Right-click the affected VM → **Manage Snapshots**
3. Select the baseline snapshot → **Restore**
4. Start the VM and verify:
   - Correct IP address is assigned
   - Network connectivity to its VLAN gateway
   - Required services are running

### Full Rebuild — Kali Linux (if snapshot unavailable)

1. Download the latest Kali Linux ARM64 image from `https://www.kali.org/get-kali/`
2. Create a new UTM VM — ARM64 architecture
3. Assign the VM's network adapter to the Mac's **en8** interface (VLAN 20)
4. Complete installation and assign static IP `192.168.20.20`, gateway `192.168.20.1`
5. Install toolset: `sudo apt update && sudo apt full-upgrade -y && sudo apt install -y kali-linux-default`
6. Verify VLAN 30 reachable: `ping 192.168.30.10` — should succeed
7. Verify VLAN 10 blocked: `ping 192.168.10.1` — should fail
8. Take a new baseline snapshot in UTM

### Full Rebuild — Metasploitable 2 (if snapshot unavailable)

1. Download Metasploitable 2 from `https://sourceforge.net/projects/metasploitable/`
2. Import the VMDK into UTM
3. Assign the VM's network adapter to the Mac's **en9** interface (VLAN 30)
4. Assign static IP `192.168.30.20`, gateway `192.168.30.1`
5. Verify services: run `nmap -sV 192.168.30.20` from Kali — should show multiple open ports
6. Take a new baseline snapshot in UTM

### Full Rebuild — Windows 11 (if snapshot unavailable)

Windows 11 VM should be restored from the WD drive VM export if the UTM snapshot is unavailable. A full OS reinstall from scratch requires a valid Windows license and is a last resort only. Always attempt export restore before considering a fresh install.

### Full Rebuild — Ubuntu Server VM (if snapshot unavailable)

1. Create a new UTM VM with Ubuntu Server
2. Assign the VM's network adapter to the Mac's **en6** interface (shared with Mac host, VLAN 10)
3. Assign static IP `192.168.10.4`, gateway `192.168.10.1`
4. Reinstall required services
5. Take a new baseline snapshot in UTM

---

## Recovery Scenario 3: Switch Misconfiguration

### Netgear Core Switch Recovery

If VLAN assignments are corrupted or management access is lost:

1. Connect a laptop directly to **Netgear Port 2 or Port 3** — both are VLAN 10 access ports, physically empty and reserved for this purpose
2. Configure the laptop with a static IP in the 192.168.10.0/24 range (e.g., `192.168.10.50`)
3. Access the Netgear web GUI at `http://192.168.10.2`
4. Log in using credentials from the encrypted 7-Zip archive
5. Restore port VLAN assignments per [06 — Port Mapping](06-port-mapping.md)

### Cisco Catalyst 2960 Recovery

**Console access tool:** Tera Term 5
**Console cable:** Cisco management (rollover) cable

1. Connect the Cisco management cable from the laptop's USB/serial adapter to the **Cisco 2960 console port**
2. Open Tera Term 5 and select the correct COM port
3. Serial settings: **9600 baud, 8 data bits, no parity, 1 stop bit, no flow control**
4. Press Enter to access the CLI
5. Log in using credentials from the encrypted 7-Zip archive
6. Restore VLAN and port configuration per [06 — Port Mapping](06-port-mapping.md)

**Key Cisco commands for verification:**
```
show vlan brief              ! Verify VLAN assignments
show interfaces trunk        ! Verify trunk ports (Port 3)
show interfaces status       ! Verify all port states
```

---

## Recovery Scenario 4: Full Environment Rebuild

**Scenario:** Catastrophic failure requiring complete rebuild of the lab from scratch.

### Rebuild Order

Sequence matters — each layer depends on the one below it being operational.

| Step | Component | Dependency |
|------|-----------|------------|
| 1 | OPNsense Firewall | None — restore from XML backup first |
| 2 | Netgear Core Switch | OPNsense must be up for VLAN routing |
| 3 | Cisco Catalyst 2960 | Netgear trunk must be active |
| 4 | Mac Server NIC verification | Switch ports must be assigned correctly |
| 5 | Ubuntu Server VM | VLAN 10 must be active |
| 6 | Victim VMs (Windows 11, Metasploitable 2) | VLAN 30 must be active |
| 7 | Kali Linux VM | VLAN 20 must be active |
| 8 | iMac Ubuntu Server | TP-Link AP and VLAN 30 must be active |
| 9 | Cisco AP | VLAN 40 and Cisco switch must be active |

---

## Post-Rebuild Verification Checklist

Run these tests in order after any full rebuild or major configuration change:

**Firewall and routing:**
- [ ] OPNsense GUI accessible from VLAN 10: `https://192.168.10.1`
- [ ] All VLAN interfaces show as active: **Interfaces → Overview**

**VLAN 10 — Management:**
- [ ] Netgear switch GUI accessible: `http://192.168.10.2`
- [ ] Ubuntu Server SSH accessible: `ssh user@192.168.10.4`
- [ ] Cisco switch reachable: `ping 192.168.10.5` from VLAN 10

**VLAN 20 → VLAN 30 (permitted):**
- [ ] Ping from Kali to Windows 11: `ping 192.168.30.10` — should succeed
- [ ] Ping from Kali to Metasploitable: `ping 192.168.30.20` — should succeed

**VLAN 20 → VLAN 10 (blocked):**
- [ ] Ping from Kali to OPNsense management interface: `ping 192.168.10.1` — should fail

**VLAN 30 egress (blocked):**
- [ ] Ping from Windows 11 to Kali: `ping 192.168.20.20` — should fail
- [ ] Ping from Windows 11 to OPNsense management interface: `ping 192.168.10.1` — should fail

**VLAN 40 isolation:**
- [ ] Ping from Cisco AP to OPNsense management interface: `ping 192.168.10.1` — should fail

**Emergency access:**
- [ ] VLAN 99 reachable via direct laptop connection: `https://192.168.99.1` from 192.168.99.50

**iMac Ubuntu Server:**
- [ ] Wireless connection active via TP-Link AP
- [ ] DHCP lease obtained on 192.168.30.x
- [ ] Web application reachable from VLAN 30
