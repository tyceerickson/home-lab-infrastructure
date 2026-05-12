# Switch Port Mapping

## Overview

This document records the physical port assignments for both managed switches in the lab — the Netgear core switch and the Cisco Catalyst 2960. Each port entry includes its VLAN assignment, mode (access or trunk), connected device, and the rationale for that configuration.

Port mapping is a critical reference for physical troubleshooting, lab reconfiguration, and onboarding documentation.

---

## Netgear Managed Core Switch (192.168.10.2)

The Netgear switch is the central distribution point for the lab. It connects directly to the OPNsense firewall via an 802.1Q trunk and distributes VLAN-tagged traffic to all physical devices and hypervisor NICs.

| Port | Mode | VLAN(s) | Connected Device | Notes |
|------|------|---------|-----------------|-------|
| 1 | Access | 30 | TP-Link AP | Victim segment — intentionally vulnerable AP |
| 2 | Access | 10 | — (empty) | Reserved for future management device |
| 3 | Access | 10 | — (empty) | Reserved for future management device |
| 4 | Trunk | 10, 40 | Cisco Catalyst 2960 | Uplink to enterprise switch — carries management and enterprise VLANs |
| 5 | Access | 10 | Mac Server (en6) — host + Ubuntu Server VM | VLAN 10 access for both the macOS host (192.168.10.3) and the Ubuntu Server VM (192.168.10.4), which share this NIC |
| 6 | Access | 30 | Mac Server (en9) | Victim VM NIC — Windows 11 and Metasploitable |
| 7 | Access | 20 | Mac Server (en8) | Attacker VM NIC — Kali Linux |
| 8 | Trunk | 10, 20, 30, 40 | OPNsense Firewall | Uplink to firewall — full VLAN trunk |

**Design Notes:**

- **Ports 5, 6, 7** connect to three separate physical NICs on the Mac Server hypervisor, providing hardware-enforced VLAN isolation for VMs. Port 5 (en6) is shared between the macOS host (192.168.10.3) and the Ubuntu Server VM (192.168.10.4) — both reach VLAN 10 through this single access port. Port 6 (en9) carries victim VM traffic (Windows 11, Metasploitable). Port 7 (en8) carries attacker VM traffic (Kali Linux).
- **Port 8** is the firewall uplink and carries all four production VLANs as a tagged trunk. OPNsense terminates each VLAN as a separate logical interface.
- **Port 4** trunks only VLANs 10 and 40 to the Cisco switch — VLAN 20 (Attackers) and VLAN 30 (Victims) are not passed downstream to the enterprise switch.
- The Emergency VLAN 99 interface is **not switched** — it is a direct physical connection from a laptop to a dedicated OPNsense interface, bypassing the Netgear switch entirely.

---

## Cisco Catalyst 2960 Plus SI PoE-8 (192.168.10.5)

The Cisco 2960 serves as the enterprise segment distribution switch. It provides PoE power to the Cisco AP and handles VLAN 40 access-layer connectivity. The switch is Layer 2 only — all routing is handled upstream by OPNsense.

**Port layout:** Ports 1–12 are PoE-capable. Ports 13–24 are standard (no PoE).

| Port | Mode | VLAN(s) | Connected Device | Notes |
|------|------|---------|-----------------|-------|
| 1 | Access | 40 | Cisco AP | PoE enabled — enterprise wireless |
| 3 | Trunk | 10, 40 | Netgear Core Switch | Uplink — receives VLAN 10 and 40 from core |
| 13 | Access | 1 | Direct laptop access | Console/management access via direct Ethernet — VLAN 1 native |

**Design Notes:**

- **Port 1** delivers both power (PoE) and VLAN 40 data to the Cisco AP. The AP requires no separate power adapter.
- **Port 3** is the uplink to the Netgear switch. Trunk carries VLAN 10 (management) and VLAN 40 (enterprise) — matching the Netgear Port 4 configuration. These must be in sync; a mismatch will cause one or both VLANs to drop.
- **Port 13** provides a direct-connect management path for the Cisco switch itself. Connecting a laptop to Port 13 on VLAN 1 allows switch CLI access without traversing the rest of the lab network. This is the equivalent of VLAN 99 for the switch layer.
- All other ports are currently unassigned.

---

## OPNsense Firewall Interface Mapping

Included here for reference alongside the switch documentation.

| Interface | Connection | Notes |
|-----------|-----------|-------|
| WAN | Eero Modem/Router | Receives upstream DHCP from Eero (192.168.4.x) |
| LAN (trunk) | Netgear Switch Port 8 | 802.1Q trunk — carries VLANs 10/20/30/40 as sub-interfaces |
| Emergency | Direct laptop connection | Dedicated physical interface — 192.168.99.1/24, not connected to any switch |

---

## Physical Cabling Summary

| Cable | From | To | VLAN(s) |
|-------|------|----|---------|
| 1 | OPNsense WAN | Eero LAN | — (upstream) |
| 2 | OPNsense LAN | Netgear Port 8 | Trunk: 10/20/30/40 |
| 3 | Netgear Port 4 | Cisco Port 3 | Trunk: 10/40 |
| 4 | Netgear Port 5 | Mac Server en6 | 10 (Management) — host + Ubuntu Server VM |
| 5 | Netgear Port 6 | Mac Server en9 | 30 (Victims) |
| 6 | Netgear Port 7 | Mac Server en8 | 20 (Attackers) |
| 7 | Netgear Port 1 | TP-Link AP | 30 (Victims) |
| 8 | Cisco Port 1 | Cisco AP | 40 (Enterprise) — PoE |
