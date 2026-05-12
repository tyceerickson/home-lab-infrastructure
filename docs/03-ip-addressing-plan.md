# IP Addressing Plan

## Overview

The lab uses a structured, predictable IP addressing scheme across all five VLANs. Each VLAN occupies a dedicated /24 subnet in the 192.168.x.0/24 range. Within each subnet, address blocks are assigned by device function — gateways, infrastructure, static hosts, and DHCP pools each occupy non-overlapping ranges.

This scheme is designed for operational clarity: given any IP address in the lab, its VLAN membership, device category, and OS type can be inferred without consulting a reference document.

---

## Subnet Allocation

| VLAN | Name | Subnet | Gateway |
|------|------|--------|---------|
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Attackers | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Victims | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Enterprise | 192.168.40.0/24 | 192.168.40.1 |
| 99 | Emergency | 192.168.99.0/24 | 192.168.99.1 |

---

## Address Block Structure (Per VLAN)

Each /24 subnet is internally divided into four functional blocks:

| Block | Range | Purpose |
|-------|-------|---------|
| Gateway | 192.168.x.1 | OPNsense VLAN interface (always .1) |
| Infrastructure | 192.168.x.2 – .9 | Network devices: switches, APs, firewalls |
| Static Hosts | 192.168.x.10 – .99 | Servers, VMs, and any device requiring a fixed IP |
| DHCP Pool | 192.168.x.100 – .200 | Dynamically assigned clients |

---

## Device Type Sub-Blocks (Static Host Range)

Within the static host range (.10–.99), addresses are further organized by operating system / device type:

| Sub-Block | Range | Device Type |
|-----------|-------|-------------|
| Windows | .10 – .19 | Windows hosts and VMs |
| Linux | .20 – .29 | Linux hosts and VMs |
| Apple / macOS | .30 – .39 | Apple hardware and macOS VMs |
| Cisco / Enterprise | .40 – .49 | Cisco devices and enterprise equipment |

This sub-block scheme ensures that an IP like `192.168.30.20` immediately communicates: VLAN 30 (Victims), Linux device — which is correct for the Metasploitable VM.

---

## Assigned Addresses

### VLAN 10 — Management (192.168.10.0/24)

| IP | Device | Type | Block |
|----|--------|------|-------|
| 192.168.10.1 | OPNsense Firewall | Gateway | Gateway |
| 192.168.10.2 | Netgear Core Switch | Infrastructure | Infra |
| 192.168.10.3 | Mac Server (UTM Hypervisor) | Static Host | Apple (.30s not used — .3 reserved as primary mgmt IP) |
| 192.168.10.4 | Ubuntu Server | Static Host | Linux |
| 192.168.10.5 | Cisco Catalyst 2960 | Infrastructure | Infra |

### VLAN 20 — Attackers (192.168.20.0/24)

| IP | Device | Type | Block |
|----|--------|------|-------|
| 192.168.20.1 | OPNsense Firewall | Gateway | Gateway |
| 192.168.20.20 | Kali Linux VM | Static Host | Linux |

### VLAN 30 — Victims (192.168.30.0/24)

| IP | Device | Type | Block |
|----|--------|------|-------|
| 192.168.30.1 | OPNsense Firewall | Gateway | Gateway |
| 192.168.30.2 | TP-Link AP | Infrastructure | Infra |
| 192.168.30.10 | Windows 11 VM | Static Host | Windows |
| 192.168.30.20 | Metasploitable VM | Static Host | Linux |
| 192.168.30.x | iMac Ubuntu Server | DHCP | DHCP Pool |

### VLAN 40 — Enterprise (192.168.40.0/24)

| IP | Device | Type | Block |
|----|--------|------|-------|
| 192.168.40.1 | OPNsense Firewall | Gateway | Gateway |
| 192.168.40.2 | Cisco AP | Infrastructure | Infra |

### Emergency VLAN 99

| IP | Device | Type | Notes |
|----|--------|------|-------|
| 192.168.99.1 | OPNsense Firewall | Interface | Direct connection only |
| 192.168.99.50 | Authorized Laptop | Allowlisted Host | Only permitted host on this segment |

---

## Address Space Utilization

| VLAN | Assigned (Static) | DHCP Pool | Reserved | Total Used |
|------|-------------------|-----------|----------|------------|
| 10 | 5 | 0 | 0 | 5 / 254 |
| 20 | 1 | 0 | 0 | 1 / 254 |
| 30 | 3 + DHCP | 101 | 0 | ~4 / 254 |
| 40 | 1 | 0 | 0 | 1 / 254 |
| 99 | 2 | 0 | 0 | 2 / 254 |

All subnets have substantial headroom for future device additions.

---

## Design Notes

**Why /24 for all subnets?**
A /24 per VLAN is intentionally oversized for the current device count. This provides room for lab expansion without renumbering and simplifies firewall rules — VLAN membership maps cleanly to a single subnet, making ACL authoring unambiguous.

**Why consistent .1 gateways?**
Using 192.168.x.1 as the gateway for every VLAN reduces configuration errors and makes the addressing scheme self-documenting. Any device in the lab can determine its default gateway from its own IP without additional reference.

**Mac Server addressing note:**
The Mac Server (192.168.10.3) does not follow the Apple sub-block convention (.30–.39) because it is a management infrastructure device — its primary identity is as a hypervisor in the management plane, not as a client host. The device type sub-blocks apply to the static host range and are most relevant for VMs and end-user devices.
