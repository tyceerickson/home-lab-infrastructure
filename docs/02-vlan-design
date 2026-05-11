# VLAN Design & Network Segmentation

## Overview

The lab network is divided into five distinct VLAN segments, each representing a different trust zone. Segmentation is enforced at the firewall level — OPNsense handles all inter-VLAN routing, ensuring every cross-segment packet is subject to stateful inspection and explicit policy evaluation before forwarding.

This architecture follows the **principle of least privilege** at the network layer: no segment communicates with another unless there is a documented operational requirement. All unspecified traffic is denied by default.

---

## VLAN Summary Table

| VLAN ID | Name | Subnet | Gateway | Trust Level | Internet Access |
|---------|------|--------|---------|-------------|-----------------|
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 | Trusted | Yes (via OPNsense) |
| 20 | Attackers | 192.168.20.0/24 | 192.168.20.1 | Untrusted | No |
| 30 | Victims | 192.168.30.0/24 | 192.168.30.1 | Isolated | No |
| 40 | Enterprise | 192.168.40.0/24 | 192.168.40.1 | Restricted | No |
| 99 | Emergency | 192.168.99.0/24 | 192.168.99.1 | Out-of-Band | No |

---

## Segment Definitions

### VLAN 10 — Trusted Management Network

**Subnet:** 192.168.10.0/24
**Gateway:** 192.168.10.1 (OPNsense)
**Color Code:** Blue

**Purpose:**
VLAN 10 is the administrative control plane. All physical infrastructure — switches, the hypervisor, and the Ubuntu services server — is managed exclusively from this segment. It is the only VLAN with unrestricted access to all other segments, functioning as an administrative override layer.

**Devices:**

| Device | IP | Role |
|--------|----|------|
| OPNsense Firewall | 192.168.10.1 | Gateway / policy enforcement |
| Netgear Core Switch | 192.168.10.2 | Core distribution |
| Mac Server (UTM) | 192.168.10.3 | Hypervisor |
| Ubuntu Server | 192.168.10.4 | Services / SSH / future SIEM |
| Cisco Catalyst 2960 | 192.168.10.5 | PoE / enterprise switch |

**Security Justification:**
Management traffic must be isolated from lab traffic. A Kali VM operating in VLAN 20 should never be able to reach switch management interfaces, the firewall GUI, or the hypervisor console — even if the attacker fully compromises a victim host. VLAN 10 enforces this boundary at the network layer, independent of host-level controls.

**Allowed Outbound:** All VLANs (administrative override)
**Allowed Inbound:** None from any other VLAN

---

### VLAN 20 — Untrusted Attackers Network

**Subnet:** 192.168.20.0/24
**Gateway:** 192.168.20.1 (OPNsense)
**Color Code:** Red

**Purpose:**
VLAN 20 hosts offensive security tooling. It is the origin point for all simulated attacks, penetration testing exercises, and exploit development. This segment is treated as untrusted by all other zones except VLAN 30 (Victims) and VLAN 40 (Enterprise), which it is permitted to reach for operational lab purposes.

**Devices:**

| Device | IP | Role |
|--------|----|------|
| Kali Linux VM | 192.168.20.20 | Attacker / offensive tooling |

**Security Justification:**
Placing the attacker in a dedicated VLAN, rather than on the same segment as victims, enforces deliberate traffic control. All attacks must traverse the firewall, which enables logging and policy enforcement even within the lab. This also prevents an attacker VM from accidentally or intentionally reaching management infrastructure — a realistic constraint that mirrors production zero-trust principles.

**Allowed Outbound:** VLAN 30 (Victims), VLAN 40 (Enterprise)
**Allowed Inbound:** VLAN 10 (Management) only
**Explicitly Blocked:** VLAN 10 (Management)

---

### VLAN 30 — Isolated Victims Network

**Subnet:** 192.168.30.0/24
**Gateway:** 192.168.30.1 (OPNsense)
**Color Code:** Yellow

**Purpose:**
VLAN 30 is the primary target environment. It hosts intentionally vulnerable systems designed to be attacked, compromised, and analyzed. The segment is fully isolated from outbound-initiated traffic — victim hosts cannot initiate connections to any other segment or the internet. This models a realistic containment boundary and ensures that compromised systems cannot be used as pivot points to reach production infrastructure.

**Devices:**

| Device | IP | Role |
|--------|----|------|
| Windows 11 VM | 192.168.30.10 | Victim endpoint |
| Metasploitable VM | 192.168.30.20 | Vulnerable services target |
| TP-Link AP | 192.168.30.2 | Intentionally vulnerable access point |
| iMac Ubuntu Server | 192.168.30.x (DHCP) | Vulnerable web server |

**Security Justification:**
Victim systems have no legitimate reason to initiate outbound connections during lab exercises. Blocking all egress from VLAN 30 enforces this constraint and provides an additional layer of containment — if a VM is misconfigured or a reverse shell attempts to beacon out, the firewall will drop the traffic. The TP-Link AP is intentionally configured with a weak passphrase as a wireless attack target; placing it in the victim VLAN ensures its exposure is scoped appropriately.

**Allowed Outbound:** None
**Allowed Inbound:** VLAN 10 (Management), VLAN 20 (Attackers)
**Explicitly Blocked:** All outbound-initiated traffic

---

### VLAN 40 — Restricted Enterprise Zone

**Subnet:** 192.168.40.0/24
**Gateway:** 192.168.40.1 (OPNsense)
**Color Code:** Green

**Purpose:**
VLAN 40 simulates a corporate enterprise wireless environment. It hosts the Cisco AP for testing enterprise wireless protocols, authentication configurations, and network access control policies. This segment is accessible to the attacker VLAN for wireless attack simulation exercises (e.g., WPA2-Enterprise attacks, rogue AP detection) but cannot initiate connections outward.

**Devices:**

| Device | IP | Role |
|--------|----|------|
| Cisco AP | 192.168.40.2 | Enterprise wireless (PoE via Cisco 2960) |

**Security Justification:**
Separating the enterprise simulation network from both the victim network and management plane allows for realistic enterprise attack/defense scenarios without risk of cross-contamination. The Cisco 2960 switch provides PoE to the AP and trunks VLANs 10 and 40 — management access is preserved while enterprise traffic remains segmented.

**Allowed Outbound:** None
**Allowed Inbound:** VLAN 10 (Management), VLAN 20 (Attackers)
**Explicitly Blocked:** All outbound-initiated traffic

---

### Emergency VLAN 99 — Out-of-Band Management

**Subnet:** 192.168.99.0/24
**Interface IP:** 192.168.99.1 (OPNsense direct)
**Allowed Host:** 192.168.99.50/32 only
**Color Code:** Gray

**Purpose:**
VLAN 99 provides a break-glass recovery path to the OPNsense firewall in the event that a misconfigured rule locks out all VLAN 10 management access. It is a physically separate interface on the firewall device, accessible via direct Ethernet connection from a laptop configured with the static IP 192.168.99.50.

**Security Justification:**
Firewall misconfigurations that block management access are a real operational risk in any environment. Rather than relying on console access alone, a dedicated out-of-band interface with a single-host allowlist provides a deterministic, documented recovery path. The /32 allowlist ensures that even if another device is connected to this interface, it cannot access the firewall GUI — only the pre-authorized host IP is permitted.

**Allowed Access:** 192.168.99.50/32 → 192.168.99.1 (firewall GUI) only
**All other traffic:** Rejected

---

## Inter-VLAN Communication Matrix

The following matrix summarizes permitted traffic flows. A checkmark indicates the source VLAN may initiate connections to the destination VLAN. All unmarked combinations are denied by default.

| Source → Destination | VLAN 10 | VLAN 20 | VLAN 30 | VLAN 40 | VLAN 99 |
|----------------------|---------|---------|---------|---------|---------|
| **VLAN 10 (Mgmt)** | — | ✅ | ✅ | ✅ | — |
| **VLAN 20 (Attack)** | ❌ | — | ✅ | ✅ | — |
| **VLAN 30 (Victim)** | ❌ | ❌ | — | ❌ | — |
| **VLAN 40 (Enterprise)** | ❌ | ❌ | ❌ | — | — |
| **VLAN 99 (Emergency)** | — | — | — | — | — |

---

## Firewall Enforcement Model

All inter-VLAN routing is handled exclusively by OPNsense. There is no switch-level inter-VLAN routing configured on either the Netgear or Cisco switches. This design choice centralizes policy enforcement and ensures:

1. All cross-segment traffic is logged at a single point
2. Stateful inspection is applied to every inter-VLAN flow
3. Rule changes take effect immediately across all segments without switch reconfiguration
4. A single firewall rule audit covers the entire lab network

The Cisco Catalyst 2960 operates as a Layer 2 only switch. Its VLAN 40 gateway is handled by OPNsense (192.168.40.1), not the switch itself.
