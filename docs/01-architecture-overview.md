# Architecture Overview

## Purpose

This repository documents the design, configuration, and security policy of a purpose-built home cybersecurity lab. The lab is engineered to support hands-on offensive security research, defensive monitoring, and enterprise network simulation in a fully isolated, controlled environment.

The lab serves as the practical foundation for coursework and independent research aligned with the **CMU MSISPM** program and cybersecurity internship work. All attack simulations, vulnerability testing, and network experiments are conducted entirely within isolated segments — no lab traffic reaches production systems or the internet.

---

## Design Objectives

| # | Objective | Implementation |
|---|-----------|----------------|
| 1 | Enforce strict network segmentation | Four-VLAN architecture with firewall-enforced inter-VLAN policy |
| 2 | Simulate realistic attacker → victim interaction | Dedicated attacker VLAN (20) with controlled access to victim VLAN (30) |
| 3 | Prevent lateral movement to management infrastructure | Explicit deny rules blocking all VLANs from reaching VLAN 10 |
| 4 | Provide isolated enterprise network simulation | VLAN 40 with Cisco AP and enterprise wireless configuration |
| 5 | Maintain out-of-band emergency access | Emergency VLAN 99 with single-host allowlist (192.168.99.50 only) |

---

## Network Topology Summary

```
Internet
    │
    ▼
Eero Modem/Router (192.168.4.1) — WAN uplink
    │
    ▼
OPNsense Firewall — Inter-VLAN routing, stateful inspection, primary policy enforcement
    │
    ▼
Netgear Managed Core Switch (192.168.10.2) — Trunk: VLANs 10/20/30/40
    │
    ├──▶ VLAN 10 — Trusted Management     (192.168.10.0/24)
    │       Mac Server (UTM hypervisor)   192.168.10.3
    │       Ubuntu Server (services/SSH)  192.168.10.4
    │       Cisco Switch (PoE/enterprise) 192.168.10.5
    │
    ├──▶ VLAN 20 — Untrusted Attackers    (192.168.20.0/24)
    │       Kali Linux VM                 192.168.20.20
    │
    ├──▶ VLAN 30 — Isolated Victims       (192.168.30.0/24)
    │       Windows 11 VM                 192.168.30.10
    │       Metasploitable VM             192.168.30.20
    │       TP-Link AP (vulnerable)       192.168.30.2
    │       iMac Ubuntu Server            192.168.30.x (DHCP via TP-Link)
    │
    ├──▶ VLAN 40 — Restricted Enterprise  (192.168.40.0/24)
    │       Cisco AP (enterprise)         192.168.40.2
    │
    └──▶ Emergency VLAN 99               (192.168.99.0/24)
            Firewall direct access        192.168.99.1
            Allowed host only             192.168.99.50
```

---

## VLAN Security Model

The lab uses a **deny-by-default, allow-by-exception** firewall policy. Each VLAN is treated as a distinct trust zone. Inter-VLAN communication is permitted only where operationally required and explicitly justified.

| VLAN | Name | Trust Level | Initiates Outbound? | Receives from VLAN 10? |
|------|------|-------------|----------------------|------------------------|
| 10 | Management | Trusted | Yes — full access | N/A (source) |
| 20 | Attackers | Untrusted | Yes — to VLANs 30/40 only | Yes |
| 30 | Victims | Isolated | No | Yes |
| 40 | Enterprise | Restricted | No | Yes |
| 99 | Emergency | Out-of-band | No | Single host only |

---

## Physical Infrastructure

| Device | Role | Management IP | VLAN |
|--------|------|---------------|------|
| Eero Modem/Router | WAN uplink / ISP gateway | 192.168.4.1 | — |
| OPNsense Firewall | Gateway, routing, policy enforcement | 192.168.10.1 | 10/20/30/40/99 |
| Netgear Managed Switch | Core distribution switch | 192.168.10.2 | Trunk |
| Cisco Catalyst 2960 | PoE switch, enterprise segment | 192.168.10.5 | Trunk (10/40) |
| Mac Server (UTM) | Hypervisor — hosts all VMs | 192.168.10.3 | 10/20/30 (multi-NIC) |
| Ubuntu Server | Services, SSH, future SIEM | 192.168.10.4 | 10 |
| Cisco AP | Enterprise wireless (VLAN 40) | 192.168.40.2 | 40 |
| TP-Link AP | Intentionally vulnerable wireless target | 192.168.30.2 | 30 |

---

## Virtual Infrastructure (UTM on Mac Server)

| VM | Role | IP | VLAN |
|----|------|----|------|
| Kali Linux | Attacker — offensive tooling | 192.168.20.20 | 20 |
| Windows 11 | Victim — endpoint target | 192.168.30.10 | 30 |
| Metasploitable | Vulnerable service target | 192.168.30.20 | 30 |
| iMac Ubuntu Server | Vulnerable web server (DHCP) | 192.168.30.x | 30 |

---

## Key Design Decisions

**Hypervisor multi-NIC isolation:** The Mac Server (UTM hypervisor) is physically connected to three separate switch ports — each assigned to a different VLAN (10, 20, 30). This ensures VM traffic enters the correct VLAN at the physical layer, not through software bridging alone. A Kali VM on interface `en8` cannot reach VLAN 10 regardless of its virtual network configuration.

**OPNsense as single policy enforcement point:** All inter-VLAN routing passes through OPNsense. There is no direct switch-level inter-VLAN routing. This centralizes visibility and ensures every cross-segment packet is subject to stateful inspection and logging.

**Victim VLAN egress lockdown:** VLAN 30 hosts cannot initiate any outbound connection — not to management, not to the attacker segment, not to the internet. This models a realistic compromised host scenario where C2 beaconing is detectable and containable.

**Emergency VLAN 99 as break-glass access:** A misconfigured firewall rule that locks out management access is a real operational risk in any lab environment. VLAN 99 is a physically separate interface on the firewall with a single allowed IP (192.168.99.50/32). This provides deterministic recovery access regardless of VLAN 10 rule state.

---

## Documentation Index

| Document | Description |
|----------|-------------|
| [02 — VLAN Design](02-vlan-design.md) | Segment definitions, trust model, security justification |
| [03 — IP Addressing Plan](03-ip-addressing-plan.md) | Subnet allocations, device type blocks, DHCP ranges |
| [04 — Firewall Rules](04-firewall-rules.md) | Per-interface rules with policy rationale |
| [05 — Device Inventory](05-device-inventory.md) | Full hardware and VM inventory |
| [06 — Port Mapping](06-port-mapping.md) | Switch port assignments, trunk configurations |
| [07 — Security Design Philosophy](07-security-design-philosophy.md) | Design rationale and threat modeling |
| [08 — Recovery Procedures](08-recovery-procedures.md) | Emergency access, restore steps, backup strategy |
| [09 — Future Roadmap](09-future-roadmap.md) | Planned projects and capability expansions |
