# Diagrams

This folder contains three network diagrams documenting the lab architecture at different levels of detail and focus. Each diagram serves a distinct purpose and is referenced from the main documentation.

---

## Architecture Diagram

**File:** `architecture-diagram.png`

![Architecture Diagram](architecture-diagram.png)

**Purpose:** High-level topology view showing all five VLAN segments, device placement within each segment, and the physical infrastructure stack (Eero → OPNsense → Netgear switch → VLANs).

**What to look for:**
- The core infrastructure stack (top center) — Eero, OPNsense, and Netgear switch form the backbone of the lab
- Emergency VLAN 99 (top right) — out-of-band management interface, physically separate from the switched network
- Color-coded VLAN zones showing trust level at a glance
- The TP-Link AP inside VLAN 30 with an arrow to the iMac Ubuntu Server — showing the wireless path for the physical victim machine

**Color legend:**

| Color | Trust Zone |
|-------|-----------|
| Blue | VLAN 10 — Trusted Management |
| Red/Pink | VLAN 20 — Untrusted Attackers |
| Yellow | VLAN 30 — Isolated Victims |
| Green | VLAN 40 — Restricted Enterprise |
| Gray | Emergency VLAN 99 / Core Infrastructure |

**Design objectives shown:**
1. Enforce strict network segmentation
2. Simulate attacker → victim interaction
3. Prevent lateral movement to management network
4. Provide isolated enterprise test network

---

## Attack Flow Diagram

**File:** `attack-flow-diagram.png`

![Attack Flow Diagram](attack-flow-diagram.png)

**Purpose:** Operational view showing which traffic flows are permitted and which are blocked, with active attack simulation paths labeled. This diagram demonstrates the firewall policy in action rather than just the static topology.

**What to look for:**
- **Red solid arrows** — active attack paths (permitted by firewall policy)
- **Red dashed lines + X** — blocked attack paths (denied at OPNsense)
- **Black solid lines** — standard network connections
- **Black dashed lines + X** — blocked network paths

**Key flows illustrated:**

| Flow | Direction | Status |
|------|-----------|--------|
| Nmap Scan | Kali → Windows 11 | ✅ Permitted (VLAN 20 → VLAN 30) |
| Exploit + C2 Attempt | Kali → Metasploitable | ✅ Permitted (VLAN 20 → VLAN 30) |
| Bot Attack | Kali → Metasploitable | ✅ Permitted (VLAN 20 → VLAN 30) |
| Traffic Interception | Kali → iMac Ubuntu (wireless) | ✅ Permitted (VLAN 30 scope) |
| Lateral movement to VLAN 10 | Any → Management | ❌ Blocked |
| Lateral movement to VLAN 40 | VLAN 30 → Enterprise | ❌ Blocked |

**Firewall rules summarized on diagram:**
- Allow: VLAN 20 → VLAN 30
- Block: VLAN 20 → VLAN 10
- Block: VLAN 30 → VLAN 10/20/40
- Block: VLAN 40 → VLAN 10/20/30

---

## Visual Network Diagram

**File:** `visual-network-diagram.png`

![Visual Network Diagram](visual-network-diagram.png)

**Purpose:** Clean, simplified hierarchical view of the network optimized for readability. Shows the logical flow from WAN uplink down through the firewall and switch to each VLAN segment, without the complexity of the attack flow annotations.

**What to look for:**
- Linear hierarchy from Eero → OPNsense → Netgear switch makes the traffic flow path immediately clear
- VLAN boxes show each segment's subnet and member devices at a glance
- Emergency VLAN 99 is shown as a separate branch with its restricted access noted
- Dashed red border on VLAN 20 indicates restricted/untrusted status
- The TP-Link AP → iMac Ubuntu arrow within VLAN 30 shows the wireless delivery path for the physical victim machine

**Legend (bottom of diagram):**

| Color | Segment |
|-------|---------|
| Blue | Management (VLAN 10) |
| Pink/Red | Attacker (VLAN 20) |
| Tan/Yellow | Victims (VLAN 30) |
| Teal/Green | Enterprise (VLAN 40) |
| Pink outline | Emergency (VLAN 99) |
| Red dashed border | Restricted access |
