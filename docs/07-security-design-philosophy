# Security Design Philosophy

## Overview

This document explains the reasoning behind the architectural and policy decisions made in this lab. Every design choice reflects a deliberate security principle — not convenience, not defaults, and not "good enough." The goal is to build a lab environment that enforces realistic security constraints, produces meaningful results during exercises, and serves as a demonstrable foundation for professional security work.

---

## Core Philosophy: Security by Design, Not by Accident

The most important principle guiding this lab is that security controls are **built into the architecture**, not bolted on afterward. This distinction matters enormously in practice:

- A firewall rule that blocks lateral movement is more reliable than a host-based control that can be disabled by a compromised process
- A physical NIC assignment that enforces VLAN membership cannot be bypassed by VM misconfiguration
- An out-of-band recovery path that exists before you need it is the difference between a recoverable incident and a full rebuild

Every major design decision in this lab was made by asking: *what happens if this component is compromised or misconfigured? Does the overall security posture degrade gracefully, or does it collapse?*

---

## Decision 1: OPNsense as the Single Policy Enforcement Point

**Decision:** All inter-VLAN routing is handled exclusively by OPNsense. Neither the Netgear nor the Cisco switch performs any Layer 3 routing.

**Rationale:**
Distributing routing across multiple devices distributes the attack surface and the audit burden. If the Netgear switch were performing inter-VLAN routing, a misconfiguration there could silently bypass firewall policy. By forcing all routed traffic through OPNsense, there is exactly one place where access control is enforced, one place where logs are generated, and one place that needs to be audited.

This also models how mature enterprise networks are architected — centralized policy enforcement with distributed switching, not distributed enforcement with unclear ownership.

**Tradeoff acknowledged:** A single enforcement point is also a single point of failure. This is acceptable in a lab context and is mitigated by Emergency VLAN 99 (see Decision 6).

---

## Decision 2: Four-Zone VLAN Segmentation Model

**Decision:** The network is divided into four distinct trust zones — Management (10), Attackers (20), Victims (30), and Enterprise (40) — rather than a flat network or a simple two-zone split.

**Rationale:**
A flat network would allow any device to reach any other device, making controlled attack simulation impossible and management infrastructure permanently exposed. A simple two-zone split (lab vs. management) would conflate the attacker and victim roles, preventing meaningful traffic analysis — you could not distinguish attack-originated traffic from victim-originated traffic at the firewall.

The four-zone model reflects how real enterprise networks are segmented: by trust level and function, not just by "internal vs. external." Each zone has a documented trust level, a defined set of permitted flows, and a rationale for why those flows exist. This is the foundation of a zero-trust-aligned architecture.

**Why a dedicated Attacker VLAN matters:**
Placing Kali Linux in its own VLAN (rather than on the same segment as victims) means every attack must traverse the firewall. This produces logs, enables detection exercises, and enforces the principle that even authorized offensive activity is monitored and controlled.

---

## Decision 3: Deny-by-Default Firewall Posture

**Decision:** Every firewall interface operates on a deny-by-default basis. All permitted flows are explicitly defined. No traffic is allowed by implication.

**Rationale:**
Allowlist-based access control is a fundamental security principle. In a deny-by-default model, adding a new device to the network does not automatically grant it communication rights — an administrator must make a deliberate policy decision to permit specific flows. This prevents accidental exposure and forces documentation of every permitted communication path.

The explicit block rules in each VLAN's ruleset (e.g., VLAN 30 → VLAN 10: Block) are intentional even where the catch-all deny would cover them. Writing explicit rules serves two purposes: it makes the intended policy unambiguous to anyone reading the configuration, and it generates distinct log entries for blocked traffic that would otherwise be silently dropped.

---

## Decision 4: Hardware-Enforced VM VLAN Isolation

**Decision:** The Mac Server hypervisor uses three separate physical NICs, each connected to a different switch port and VLAN, rather than relying on virtual switch or VLAN tagging within the hypervisor.

**Rationale:**
Software-defined VLAN isolation within a hypervisor depends on the hypervisor's virtual switch behaving correctly. A misconfigured virtual network adapter, a UTM bug, or a VM escape could potentially bridge traffic across virtual segments. By binding each NIC to a single VLAN at the physical switch port, VLAN membership is enforced at the hardware layer — a Kali VM on en8 is physically incapable of sending traffic into the VLAN 10 segment regardless of its virtual network configuration.

This is a meaningful security improvement over pure software isolation, and it mirrors how production environments treat hypervisor network security: physical separation where possible, software separation only where physical separation is not feasible.

**Practical implication:** This design requires three physical NICs on the Mac Server and three dedicated switch ports on the Netgear switch. That resource cost is a deliberate investment in isolation quality.

---

## Decision 5: Victim VLAN Egress Lockdown

**Decision:** VLAN 30 (Victims) has no permitted outbound-initiated traffic to any other segment or the internet. Victims can respond to inbound connections but cannot initiate them.

**Rationale:**
This decision serves two purposes simultaneously. First, it models realistic containment: in an actual incident response scenario, a compromised host would be isolated at the network layer to prevent it from communicating with attacker infrastructure, exfiltrating data, or pivoting to other segments. Building this constraint into the lab enforces the same containment model during exercises.

Second, it makes attack detection exercises more meaningful. If a victim host cannot beacon out, any outbound traffic from VLAN 30 is immediately anomalous and detectable — there is no baseline legitimate outbound traffic to hide in. This is particularly relevant for future SIEM deployment, where alert tuning depends on having a clean baseline.

**Intentional design tension:** Preventing victims from initiating connections means some attack scenarios (e.g., reverse shells beaconing to Kali) will be blocked by the firewall. This is a feature, not a limitation — it forces the use of bind shells or other techniques that reflect real-world constrained environments, producing more realistic and educational exercises.

---

## Decision 6: Emergency VLAN 99 as Break-Glass Recovery

**Decision:** A dedicated out-of-band management interface (VLAN 99) with a single-host allowlist (192.168.99.50/32) provides recovery access to the firewall independent of all other VLAN rules.

**Rationale:**
Firewall misconfigurations that lock out management access are a realistic operational risk — particularly in a lab environment where rule experimentation is frequent. Without a recovery path, a single bad rule could require a full OPNsense console reset, destroying the running configuration. VLAN 99 is a physically separate interface on the firewall that bypasses the trunk switch entirely, meaning it is unaffected by any VLAN 10 rule state.

The /32 allowlist is the critical hardening element. A dedicated recovery interface with open access would be a significant vulnerability — any device physically connected to that port could access the firewall GUI. The /32 restriction ensures that only a laptop pre-configured with 192.168.99.50 can authenticate, even if another device is physically connected.

This design reflects a mature operational principle: **plan for failure before it happens, and make recovery deterministic.**

---

## Decision 7: Intentionally Vulnerable Targets as Dedicated Infrastructure

**Decision:** Vulnerable targets (Metasploitable, TP-Link AP, iMac Ubuntu Server) are dedicated devices in the victim VLAN rather than temporarily configured VMs spun up per exercise.

**Rationale:**
Persistent vulnerable infrastructure allows for more realistic and repeatable exercises. A Metasploitable instance that is always available means attack chains can be developed incrementally across multiple sessions without environment setup overhead. The TP-Link AP with a known weak passphrase is permanently available as a wireless attack target, enabling wireless security exercises without reconfiguration.

This also documents a real security principle: in production environments, legacy and unpatched systems often persist indefinitely due to operational constraints. Treating vulnerable targets as permanent lab infrastructure reinforces the mindset that such systems require active containment, not just isolation-by-neglect.

---

## Threat Model

The lab is designed around the following internal threat model:

| Threat | Mitigation |
|--------|-----------|
| Attacker VM reaches management infrastructure | VLAN 20 → VLAN 10 explicitly blocked; physical NIC isolation prevents bypass |
| Compromised victim pivots to management | VLAN 30 → VLAN 10 explicitly blocked; no egress from victim VLAN |
| Lab traffic reaches internet / production network | Internet access blocked on all lab VLANs; OPNsense is sole WAN gateway |
| Firewall misconfiguration locks out management | Emergency VLAN 99 with /32 allowlist provides independent recovery path |
| VM escape bridges virtual segments | Physical NIC per VLAN — hardware enforcement independent of hypervisor |
| Unauthorized access to recovery interface | /32 host allowlist on VLAN 99 — only pre-authorized IP permitted |

---

## What This Lab Is Not

This design deliberately excludes several configurations that would reduce security meaningfulness:

- **No NAT between lab VLANs** — all inter-VLAN traffic is routed and inspectable, not masqueraded
- **No split-tunneling or VPN bypass** — all traffic follows documented firewall policy
- **No shared credentials across segments** — each segment's devices are administered independently
- **No internet access from lab VLANs** — tooling is installed offline; no live attack infrastructure

These exclusions are design decisions, not limitations. Each one enforces a constraint that makes the lab more useful as a controlled research environment and more representative of production security architecture.
