# 09 — Future Roadmap & Project Portfolio Plan

Purpose: This document outlines the complete project roadmap planned for this home cybersecurity lab. It serves as a strategic reference — detailing the why, how, deliverables, and sequencing for every project. Each project builds on the last, culminating in a capstone AI-powered security operations pipeline.

Context: These projects were built to support internship applications alongside Carnegie Mellon University's MSISPM (Master of Science in Information Security Policy and Management) program, beginning Fall 2026. The portfolio is designed to demonstrate both technical depth and the management/policy communication skills that MSISPM roles demand.

> **Status note (May 2026): all four projects are complete and published.** This document preserves the original strategic plan; where the as-built implementation differs from the plan (it frequently exceeded it), see each project's own repository for what was actually delivered. Repository links are in the Portfolio Overview table below.

## Portfolio Overview
| Phase | Project | Type | Status | Repository |
|-------|---------|------|--------|------------|
| 1 | Complete Lab Documentation | Foundation | ✅ Complete | [home-lab-infrastructure](https://github.com/tyceerickson/home-lab-infrastructure) |
| 2 | AI Network Traffic Classifier | AI + Security | ✅ Complete | [ai-traffic-classifier](https://github.com/tyceerickson/ai-traffic-classifier) |
| 3 | Honeypot Deployment | Security | ✅ Complete | [honeypot-deployment](https://github.com/tyceerickson/honeypot-deployment) |
| 4 | Capstone — AI-Powered SOC Pipeline | AI + Security | ✅ Complete | [ai-soc-pipeline](https://github.com/tyceerickson/ai-soc-pipeline) |

## The Portfolio Narrative

These projects are intentionally sequenced to tell a single, cohesive story to recruiters:

- "I designed, built, and documented a real segmented network lab"
- "I generated real network traffic, then trained a local AI model to classify it as malicious or benign"
- "I deployed a honeypot in my lab and captured real attack data"
- "I built a full AI-powered SOC pipeline — log analysis, SIEM, and a live dashboard — on top of it all"

Each project stands alone as a strong GitHub repository. Together, they form a complete security engineering portfolio with AI woven throughout — rare at the pre-masters level.

> As-built note: Projects 3 and 4 ultimately exceeded the original plan below. The honeypot (Phase 3) was deployed on an internet-facing DigitalOcean VPS capturing real global attack traffic — not an internal-only VLAN 30 target — and the capstone (Phase 4) ingested live data from three internet honeypots rather than the originally planned internal log sources. The plan text below is preserved as the original design intent.

## Phase 1 — Complete Lab Documentation
### Why

Every other project in this portfolio references this lab. Without thorough, professional documentation, the projects that follow lack context and credibility. This repository is the foundation that every recruiter will see first when visiting the GitHub profile. It must demonstrate the ability to design, implement, and communicate infrastructure — a core skill for both security engineering and MSISPM policy roles.

Additionally, the documentation process itself reinforces deep understanding of every design decision made in this lab, which becomes critical when discussing the architecture in interviews.

### Goals
- Publish a complete, professional GitHub repository documenting the entire lab
- Demonstrate both technical competency (architecture, VLANs, firewall rules) and management competency (security rationale, design philosophy, policy justification)
- Create a reusable reference that all future projects link back to

### How

Working through each documentation section systematically, professionalizing existing notes, and filling gaps. Each document should read like a junior security engineer's wiki contribution — clean, structured, and justification-driven. Not a school project. Not a personal journal. A professional artifact.

### Deliverables
- README.md — Lab overview, purpose, architecture summary, quick-start navigation
- docs/01-architecture-overview.md — High-level design narrative explaining the full lab topology
- docs/02-vlan-design.md — VLAN segments with security justification for each zone
- docs/03-ip-addressing-plan.md — Subnets, device type blocks, DHCP ranges
- docs/04-firewall-rules.md — Full rules table per interface with policy rationale
- docs/05-device-inventory.md — All devices, roles, IPs, OS versions, purpose
- docs/06-port-mapping.md — Netgear and Cisco switch port assignments
- docs/07-security-design-philosophy.md — The "why" essay: design decisions, threat model, tradeoffs
- docs/08-recovery-procedures.md — Emergency VLAN usage, restore steps, backup strategy
- docs/09-future-roadmap.md — This document
- diagrams/ — Architecture diagram, attack flow diagram, visual network diagram with captions

### Success Criteria

A recruiter or MSISPM admissions reviewer should be able to read this repository and understand: what the lab is, why it is designed the way it is, what security principles it enforces, and how it is managed. No prior briefing required.

## Phase 2 — AI Network Traffic Classifier
### Why

This project demonstrates the ability to work end-to-end across a real ML pipeline — from data collection to model training to evaluation — using live traffic generated in a real lab environment. It directly addresses the "more AI skills" requirement for MSISPM internship positioning.

The use of an RTX 4070-equipped Alienware for local model training (rather than cloud APIs) is a meaningful differentiator — it shows understanding of both the AI tooling landscape and how to work without paid API dependencies.

### Goals
- Generate a labeled dataset of malicious and benign network traffic using the lab
- Build and train a local ML classification model on the Alienware laptop
- Publish the full pipeline — data, model, methodology, and results — to GitHub
- Demonstrate that AI can be applied practically to real security problems

### How

Step 1 — Traffic Generation

Malicious traffic (generated from Kali Linux, VLAN 20):

- Nmap reconnaissance scans against VLAN 30 targets
- Metasploit exploit attempts against Metasploitable (192.168.30.20)
- Password/credential attacks against vulnerable targets
- C2-style communication patterns

Benign traffic (generated across the lab): normal HTTP/HTTPS browsing, DNS queries, SSH sessions, file transfers between lab devices.

Step 2 — Capture and Label

- Capture traffic (as-built: OPNsense built-in packet capture, synced to Ubuntu Server)
- Export to PCAP, convert to flow features with CICFlowMeter
- Label each capture session (malicious / benign) using a timestamped session log

Step 3 — Preprocessing

- Extract statistical flow features (packet length, protocol, flags, inter-arrival time, etc.)
- Clean and normalize dataset using pandas
- Split into training and test sets

Step 4 — Model Training

- Train classifiers using scikit-learn (Random Forest as primary; XGBoost, Decision Tree, Logistic Regression for comparison)
- Run locally on Alienware (Intel Ultra 9 185H + RTX 4070 + 64GB RAM)
- Evaluate: accuracy, precision, recall, F1, confusion matrix

Step 5 — Documentation and Publishing

- README with methodology, architecture, results, and limitations
- Visualizations of model performance
- Writeup comparing local model approach vs. cloud AI API approach

### Technical Stack
- Python, scikit-learn, pandas, matplotlib, seaborn
- OPNsense packet capture + CICFlowMeter for flow features; Suricata (offline) for IDS alerts
- Local training on Alienware
- Ollama llama3.1:8b for local LLM explanation of classifications

### Success Criteria

Someone should be able to clone the repository and reproduce the results. The writeup should explain not just what was built, but why the design choices were made — demonstrating the management communication layer on top of the technical work.

## Phase 3 — Honeypot Deployment
### Why

This project serves two purposes simultaneously. First, it demonstrates hands-on defensive security skill — specifically deception-based detection techniques. Second, and strategically, it generates a rich corpus of real, labeled attack data that feeds directly into the Phase 4 capstone.

Building the capstone on top of real honeypot data (rather than synthetic or sample data) makes the final project substantially more credible and interesting to recruiters.

### Goals (as-built)
- Deploy a production-grade, internet-facing honeypot stack (Cowrie + nginx + Dionaea) on a DigitalOcean VPS
- Forward logs to the home lab over an encrypted WireGuard tunnel
- Capture, enrich, and analyze real global attacker behavior
- Export logs in a format ready for SIEM ingestion in Phase 4
- Produce a formal deployment and analysis report

> Original plan placed the honeypot internally on VLAN 30 targeted by Kali. The as-built deployment went further — a public VPS capturing real internet attack traffic (11.6M events over 6 days). See the honeypot-deployment repo.

### Deliverables
- Honeypot deployment documentation (setup, configuration, network placement)
- Firewall and WireGuard tunnel documentation
- Captured attack analysis report
- Attacker behavior mapped to MITRE ATT&CK
- Data export pipeline (logs formatted for Wazuh SIEM ingestion)
- Lessons learned and defensive recommendations writeup

### Success Criteria

The project produces both a readable security report and a clean, structured data export ready for the Phase 4 capstone.

## Phase 4 — Capstone: AI-Powered SOC Pipeline
### Why

This is the flagship project of the entire portfolio. It synthesizes everything built in Phases 1-3 into a single, cohesive security operations pipeline — infrastructure, AI classification, honeypot data — and wraps it in a live, functioning system that resembles a real SOC environment.

For MSISPM internship applications, this project demonstrates that AI can be understood, deployed, and communicated about not just theoretically, but in a functioning security context. It bridges the technical and management layers that define the MSISPM program.

### Goals (as-built)
- Build a functioning AI-powered SOC pipeline on real lab + honeypot infrastructure
- Demonstrate end-to-end data flow: honeypot logs → enrichment → Wazuh SIEM → AI analysis → live dashboard
- Ingest live data from three internet honeypots (Cowrie, nginx, Dionaea)
- Produce a custom Flask dashboard with threat-actor correlation, malware analysis (VirusTotal-verified WannaCry captures), and AI-generated executive summaries
- Produce publication-quality documentation

> Original plan envisioned three progressive sub-phases (AI log analyzer → SIEM + AI summarizer → dashboard) drawing from internal log sources (OPNsense, Windows 11, Metasploitable). The as-built capstone implemented all three layers and went further, using live internet-honeypot data and adding cross-honeypot threat-actor correlation and live malware capture/verification. See the ai-soc-pipeline repo.

### Capstone Deliverables (as-built)
- Full SOC dashboard codebase (Flask backend, 12 intelligence panels, 16 API endpoints)
- Wazuh SIEM deployment + AI triage layer (local Ollama llama3.1:8b)
- Malware capture pipeline (SHA256 + VirusTotal + permanent archive)
- Cross-honeypot threat-actor correlation
- Full documentation set (docs/01–09) plus executive summary
- Reflection / lessons-learned writeup

### Capstone Success Criteria

The complete capstone is demonstrable live — real data flowing from the honeypots through the pipeline to the dashboard. The documentation is thorough enough for a senior engineer to review, and the executive summary is clear enough for a non-technical hiring manager to understand what was built and why it matters.

## AI & Certification Foundation

These projects were supported by a parallel learning track:

| Credential | Platform | Purpose |
|------------|----------|---------|
| Microsoft AI-900 | Microsoft Learn | AI fundamentals, responsible AI, Azure AI services |
| Google AI Essentials | Google | Practical AI tools, prompt engineering |
| CS50 AI with Python | Harvard (edX) | Search, probability, neural networks, ML classification |
| Harvard ML with Python | Harvard (edX) | ML pipeline, model evaluation — direct prep for Phase 2 |

The CS50 AI and Harvard ML courses were intentional preparation for the AI Traffic Classifier project — specifically the classification, model evaluation, and scikit-learn concepts.

## Tools & Technologies (Across All Projects)
| Category | Tools |
|----------|-------|
| Languages | Python, Bash, HTML/CSS |
| ML / AI | scikit-learn, pandas, matplotlib, Ollama (llama3.1:8b), VirusTotal API |
| Security | Kali Linux, Metasploit, Wireshark/tshark, Nmap, Suricata, Wazuh, Cowrie, Dionaea |
| Infrastructure | OPNsense, Netgear switch, Cisco switch, UTM hypervisor, DigitalOcean, WireGuard, Tailscale |
| Development | Jupyter notebooks, Flask, Git/GitHub |
| Hardware | Alienware m16 R2 (Intel Ultra 9 185H, 64GB RAM, RTX 4070) |

## How This Document Was Used

This roadmap was the connective tissue across all four project repositories during development. Now that the portfolio is complete, the Portfolio Overview table above links each finished repository.

Last updated: May 2026 · Author: Tyce Erickson · Program: CMU MSISPM, Fall 2026
