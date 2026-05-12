# Firewall Rules Documentation

## Overview

OPNsense is the sole policy enforcement point for all inter-VLAN traffic in the lab. Rules are applied **inbound on each interface** — traffic is evaluated as it enters the firewall from a given VLAN. All interfaces follow a **deny-by-default** posture: unless a rule explicitly permits a flow, it is blocked.

Rules are listed in evaluation order. OPNsense processes rules top-to-bottom and applies the first match. Explicit block rules are placed before the final catch-all deny to ensure intentional rejection is logged distinctly from implicit drops.

---

## LAN Interface

**Status:** Disabled

The physical LAN interface is disabled. All traffic is handled through VLAN sub-interfaces. This prevents any untagged traffic from bypassing VLAN policy.

---

## VLAN 10 — Management Interface

**Status:** Enabled
**Trust Level:** Trusted — Administrative override

| # | Action | Source | Destination | Description |
|---|--------|--------|-------------|-------------|
| 1 | ✅ Pass | VLAN 10 net | This Firewall | Allow GUI and SSH access to OPNsense |
| 2 | ✅ Pass | VLAN 10 net | VLAN 20 net | Allow management access to Attacker segment |
| 3 | ✅ Pass | VLAN 10 net | VLAN 30 net | Allow management access to Victim segment |
| 4 | ✅ Pass | VLAN 10 net | VLAN 40 net | Allow management access to Enterprise segment |
| 5 | 🚫 Block | VLAN 10 net | Any | Block all other outbound traffic |

**Policy Rationale:**
VLAN 10 is the administrative control plane. Rules 1–4 grant full reach across all lab segments — this is the intentional "admin override" capability that allows infrastructure management, VM console access, and firewall configuration from trusted devices only. Rule 5 blocks internet access from the management VLAN, preventing management hosts from being used as general-purpose internet clients and reducing the risk of management traffic leaking outside the lab.

---

## VLAN 20 — Attackers Interface

**Status:** Enabled
**Trust Level:** Untrusted

| # | Action | Source | Destination | Description |
|---|--------|--------|-------------|-------------|
| 1 | ✅ Pass | VLAN 20 net | This Firewall | Allow attacker access to firewall (DNS, gateway) |
| 2 | ✅ Pass | VLAN 20 net | VLAN 30 net | Allow attacker to reach Victim network |
| 3 | ✅ Pass | VLAN 20 net | VLAN 40 net | Allow attacker to reach Enterprise network |
| 4 | 🚫 Block | VLAN 20 net | VLAN 10 net | Deny access to Management network |
| 5 | 🚫 Block | VLAN 20 net | Any | Block all other traffic (including internet) |

**Policy Rationale:**
Rules 2 and 3 are the core operational permissions — they enable the primary lab use case of simulated attacks against victim and enterprise targets. Rule 4 is a critical protection: even if an attacker VM is fully compromised or misconfigured, it cannot reach management infrastructure. Rule 1 allows the attacker to use the firewall as a DNS resolver and default gateway, which is required for normal IP stack operation. Rule 5 blocks internet access from the attacker VLAN — all tooling should be pre-installed, and outbound internet access from an untrusted segment is an unnecessary risk.

> **Hardening Note — Rule 1:** All firewall rules are currently configured as port-agnostic (all ports). Rule 1 allows the attacker to reach the firewall on any port, which includes the OPNsense management GUI. A future hardening pass should scope this rule to DNS (UDP/53) and DHCP (UDP/67-68) only, eliminating unnecessary GUI exposure from an untrusted segment.

---

## VLAN 30 — Victims Interface

**Status:** Enabled
**Trust Level:** Isolated

| # | Action | Source | Destination | Description |
|---|--------|--------|-------------|-------------|
| 1 | ✅ Pass | VLAN 30 net | This Firewall | Allow victim access to firewall (DNS, gateway) |
| 2 | 🚫 Block | VLAN 30 net | VLAN 10 net | Deny access to Management network |
| 3 | 🚫 Block | VLAN 30 net | VLAN 20 net | Deny access to Attacker network |
| 4 | 🚫 Block | VLAN 30 net | VLAN 40 net | Deny access to Enterprise network |
| 5 | 🚫 Block | VLAN 30 net | Any | Deny all other outbound traffic |

**Policy Rationale:**
Victim systems have no legitimate reason to initiate connections to any other segment. Rules 2–5 enforce complete egress isolation — a compromised victim cannot pivot to management infrastructure, cannot reach back to the attacker (preventing C2 beaconing), and cannot access the internet. This containment model is realistic: in a production incident response scenario, you would want to ensure that compromised hosts cannot communicate outside their segment. Rule 1 allows basic IP stack operation (DHCP, DNS) while all other outbound traffic is denied.

---

## VLAN 40 — Enterprise Interface

**Status:** Enabled
**Trust Level:** Restricted

| # | Action | Source | Destination | Description |
|---|--------|--------|-------------|-------------|
| 1 | ✅ Pass | VLAN 40 net | This Firewall | Allow enterprise devices access to firewall (DNS, gateway) |
| 2 | 🚫 Block | VLAN 40 net | VLAN 10 net | Deny access to Management network |
| 3 | 🚫 Block | VLAN 40 net | VLAN 20 net | Deny access to Attacker network |
| 4 | 🚫 Block | VLAN 40 net | VLAN 30 net | Deny access to Victim network |
| 5 | 🚫 Block | VLAN 40 net | Any | Deny all other outbound traffic |

**Policy Rationale:**
The enterprise segment mirrors the victim segment's isolation posture. Enterprise devices (Cisco AP, wireless clients) cannot initiate connections outside VLAN 40. This models realistic enterprise network behavior where end-user wireless clients do not have direct access to infrastructure management planes. The attacker (VLAN 20) can reach VLAN 40, enabling enterprise wireless attack simulations, while VLAN 40 devices cannot respond or pivot outward.

---

## Emergency VLAN 99 — Out-of-Band Management

**Status:** Enabled
**Trust Level:** Break-glass only

| # | Action | Source | Destination | Description |
|---|--------|--------|-------------|-------------|
| 1 | ✅ Allow | 192.168.99.50/32 | 192.168.99.1/32 | Allow single authorized host to reach firewall GUI |
| 2 | ❌ Reject | Any | Emergency VLAN net | Block all other access to this segment |

**Policy Rationale:**
The /32 source allowlist on Rule 1 is the critical control here — it ensures only the pre-configured laptop (192.168.99.50) can reach the firewall interface. Using Reject rather than Block on Rule 2 provides immediate feedback to a connecting device that access is denied, which is useful when troubleshooting physical connectivity issues during an actual emergency recovery scenario.

---

## WAN Interface

**Status:** Enabled

The WAN interface connects OPNsense to the Eero modem/router (192.168.4.1), which serves as the ISP uplink. OPNsense's default behavior blocks all unsolicited inbound WAN traffic — no custom inbound rules are currently configured beyond the implicit deny.

**Scope note:** The Eero operates on an upstream network (192.168.4.0/24) outside the lab's administrative control. No management access to the Eero is available. OPNsense is the effective security boundary — all traffic entering the lab from the WAN is subject to OPNsense stateful inspection regardless of Eero configuration.

> **Future hardening:** Explicit WAN rules (RFC1918 inbound block, anti-spoofing) are a recommended addition when OPNsense WAN rule configuration is reviewed. This is a documented future task in the roadmap.

---

## Rule Authoring Principles

1. **Deny by default** — no rule means no access. Every permitted flow is explicitly documented.
2. **Explicit over implicit** — block rules are written out even when the catch-all would cover them, for logging clarity and auditability.
3. **Least privilege** — each VLAN is granted only the access required for its operational role.
4. **Management plane protection** — VLAN 10 is unreachable from all other segments regardless of other rule logic.
5. **Egress control on untrusted segments** — internet access is blocked from all lab VLANs except where explicitly required.
