# 09 — Future Roadmap & Project Portfolio Plan

> **Purpose:** This document outlines the complete project roadmap planned for this home cybersecurity lab. It serves as a strategic reference — detailing the why, how, deliverables, and sequencing for every project. Each project builds on the last, culminating in a capstone AI-powered security operations pipeline.

> **Context:** These projects are being built to support internship applications between years of Carnegie Mellon University's MSISPM (Master of Science in Information Security Policy and Management) program, beginning Fall 2026. The portfolio is designed to demonstrate both technical depth and the management/policy communication skills that MSISPM roles demand.

---

## Portfolio Overview

| Phase | Project | Type | Status |
|-------|---------|------|--------|
| 1 | Complete Lab Documentation | Foundation | 🔄 In Progress |
| 2 | AI Network Traffic Classifier | AI + Security | 📋 Planned |
| 3 | Honeypot Deployment | Security | 📋 Planned |
| 4 | Capstone — AI-Powered SOC Pipeline | AI + Security | 📋 Planned |

---

## The Portfolio Narrative

These projects are intentionally sequenced to tell a single, cohesive story to recruiters:

1. *"I designed, built, and documented a real segmented network lab"*
2. *"I generated real network traffic, then trained a local AI model to classify it as malicious or benign"*
3. *"I deployed a honeypot in my lab and captured real attack data"*
4. *"I built a full AI-powered SOC pipeline — log analysis, SIEM, and a live dashboard — on top of it all"*

Each project stands alone as a strong GitHub repository. Together, they form a complete security engineering portfolio with AI woven throughout — rare at the pre-masters level.

---

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
- `README.md` — Lab overview, purpose, architecture summary, quick-start navigation
- `docs/01-architecture-overview.md` — High-level design narrative explaining the full lab topology
- `docs/02-vlan-design.md` — VLAN segments with security justification for each zone
- `docs/03-ip-addressing-plan.md` — Subnets, device type blocks, DHCP ranges
- `docs/04-firewall-rules.md` — Full rules table per interface with policy rationale
- `docs/05-device-inventory.md` — All devices, roles, IPs, OS versions, purpose
- `docs/06-port-mapping.md` — Netgear and Cisco switch port assignments
- `docs/07-security-design-philosophy.md` — The "why" essay: design decisions, threat model, tradeoffs
- `docs/08-recovery-procedures.md` — Emergency VLAN usage, restore steps, backup strategy
- `docs/09-future-roadmap.md` — This document
- `diagrams/` — Architecture diagram, attack flow diagram, visual network diagram with captions

### Success Criteria
A recruiter or MSISPM admissions reviewer should be able to read this repository and understand: what the lab is, why it is designed the way it is, what security principles it enforces, and how it is managed. No prior briefing required.

---

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

**Step 1 — Traffic Generation**

Malicious traffic (generated from Kali Linux, VLAN 20):
- Nmap reconnaissance scans against VLAN 30 targets
- Metasploit exploit attempts against Metasploitable (192.168.30.20)
- Password/credential attacks against vulnerable targets
- C2-style communication patterns

Benign traffic (generated across the lab):
- Normal HTTP/HTTPS browsing
- DNS queries
- SSH sessions
- File transfers between lab devices

**Step 2 — Capture and Label**
- Capture traffic using Wireshark
- Export to PCAP and CSV formats
- Label each capture session (malicious / benign) based on known generation activity

**Step 3 — Preprocessing**
- Extract features from PCAP (packet length, protocol, flags, inter-arrival time, etc.)
- Clean and normalize dataset using pandas
- Split into training and test sets

**Step 4 — Model Training**
- Train a classifier using scikit-learn (Random Forest as primary candidate)
- Run locally on Alienware (Intel Ultra 9 185H + RTX 4070 + 64GB RAM)
- Evaluate: accuracy, precision, recall, F1, confusion matrix

**Step 5 — Documentation and Publishing**
- Jupyter notebook walking through the full pipeline
- README with methodology, architecture, results, and limitations
- Visualizations of traffic patterns and model performance

### Technical Stack
- Python, scikit-learn, pandas, matplotlib, seaborn
- Wireshark / tshark for traffic capture
- Jupyter notebooks
- Local training on Alienware
- Optional: Ollama for local LLM component to explain classifications in plain English

### Deliverables
- Labeled network traffic dataset (PCAP + CSV, sanitized)
- Data preprocessing pipeline (Python scripts)
- Trained ML classifier (exported model file)
- Jupyter notebook — full pipeline walkthrough
- Model evaluation report (metrics + confusion matrix visualizations)
- README with methodology, architecture diagram, and results summary
- Writeup comparing local model approach vs. cloud AI API approach

### Success Criteria
Someone should be able to clone the repository, run the notebook, and reproduce the results. The writeup should explain not just what was built, but why the design choices were made — demonstrating the management communication layer on top of the technical work.

### Notes
- CS50 AI and the Harvard ML course are intentional preparation for this project — specifically the classification and ML evaluation concepts
- Pay extra attention to scikit-learn basics during the cert/learning phase before starting this project

---

## Phase 3 — Honeypot Deployment

### Why
This project serves two purposes simultaneously. First, it demonstrates hands-on defensive security skill — specifically deception-based detection techniques, which are increasingly relevant in modern security operations. Second, and strategically, it generates a rich corpus of real, labeled attack data that feeds directly into the Phase 4 capstone project.

Building the capstone on top of real honeypot data (rather than synthetic or sample data) makes the final project substantially more credible and interesting to recruiters.

### Goals
- Deploy a production-grade honeypot on VLAN 30 (isolated victim network)
- Capture real attack interactions using Kali as the controlled attacker
- Analyze and document attacker behavior patterns
- Export logs in a format ready for SIEM ingestion in Phase 4
- Produce a formal deployment and analysis report

### How

**Step 1 — Honeypot Selection**

Evaluate the following options against the lab architecture before committing:

| Honeypot | Type | Best For |
|----------|------|----------|
| Cowrie | SSH/Telnet | Credential attacks, session capture |
| Dionaea | Multi-protocol | Malware capture, exploit attempts |
| OpenCanary | Lightweight multi-protocol | Quick deployment, low overhead |
| T-Pot | Comprehensive platform | Full coverage, multiple honeypots |

Selection criteria: compatibility with VLAN 30 placement, log export format for Wazuh ingestion, ease of deployment on available hardware.

**Step 2 — Deployment**
- Deploy chosen honeypot on VLAN 30 (192.168.30.x)
- Modify OPNsense firewall rules as needed to allow attacker → honeypot traffic
- Verify isolation so honeypot cannot reach management (VLAN 10) or enterprise (VLAN 40) networks
- Configure logging to capture full session data

**Step 3 — Attack Simulation**
- Run controlled attacks from Kali (192.168.20.20) against the honeypot
- Document each attack type and expected log output
- Capture: credential attempts, session commands, exploit payloads, connection metadata

**Step 4 — Analysis and Documentation**
- Analyze captured logs for patterns
- Document attacker behavior: techniques observed, credentials attempted, commands run
- Map observations to MITRE ATT&CK framework where applicable

**Step 5 — Data Pipeline Prep**
- Format and export logs for SIEM ingestion (JSON / syslog format)
- This exported dataset becomes the primary data source for the Phase 4 capstone

### Deliverables
- Honeypot deployment documentation (setup, configuration, network placement)
- OPNsense firewall rule change documentation
- Captured attack log samples (sanitized)
- Attack pattern analysis report
- Attacker behavior documentation mapped to MITRE ATT&CK
- Data export pipeline (logs formatted for Wazuh SIEM ingestion)
- README with architecture diagram showing honeypot placement in lab
- Lessons learned and defensive recommendations writeup

### Success Criteria
The project should produce both a readable security report and a clean, structured data export ready for the Phase 4 capstone. A reader should understand: what was deployed, where, what it captured, and what the data means from a defensive security perspective.

---

## Phase 4 — Capstone: AI-Powered SOC Pipeline

### Why
This is the flagship project of the entire portfolio. It synthesizes everything built in Phases 1-3 into a single, cohesive security operations pipeline — infrastructure documentation, AI classification, honeypot data — and wraps it in a live, functioning system that resembles what a real SOC environment looks like.

For MSISPM internship applications, this project does something most candidates cannot: it demonstrates that AI can be understood, deployed, and communicated about not just theoretically, but in a functioning security context. It bridges the technical and management layers that define the MSISPM program.

The capstone is structured as three progressive phases, each independently impressive but stronger as a complete pipeline.

### Goals
- Build a functioning AI-powered security operations pipeline on real lab infrastructure
- Demonstrate end-to-end data flow: raw logs → AI analysis → SIEM alerting → live dashboard
- Produce publication-quality documentation for each phase
- Create a system that can be demonstrated live in an interview setting

---

### Capstone Phase 1 — AI Firewall Log Analyzer

**What it does:** Pulls OPNsense firewall logs, sends them to an AI API (OpenAI or local Ollama model), and returns plain-English threat summaries and anomaly flags.

**Why it matters:** Translating raw firewall logs into human-readable intelligence is a core SOC function. Automating it with AI demonstrates both security domain knowledge and practical AI API integration.

**How to build it:**
- Python script to extract logs from OPNsense (via syslog or log export)
- Chunk logs and send to OpenAI API with a security-focused system prompt
- Parse AI response into structured threat summary output
- Format output as clean terminal report or structured JSON

**Deliverables:**
- Python tool (well-documented, GitHub-published)
- Sample log inputs and AI-generated output examples
- README explaining the architecture and prompt design decisions
- Short writeup on AI prompt engineering choices for security log analysis

---

### Capstone Phase 2 — SIEM Deployment + AI Alert Summarizer

**What it does:** Deploys Wazuh SIEM on the Ubuntu Server, ingests logs from all major lab sources, and adds a Python AI layer that prioritizes and explains alerts in plain English.

**Why it matters:** SIEM experience is one of the most in-demand skills in security operations. Adding an AI summarization layer makes it novel and directly demonstrates how AI augments traditional security tooling.

**How to build it:**
- Deploy Wazuh on Ubuntu Server (192.168.10.4)
- Configure log ingestion from: OPNsense firewall, Windows 11 VM, Metasploitable, Honeypot (from Phase 3)
- Build Python script that queries Wazuh REST API for active alerts
- Pass alerts to AI API with severity-classification prompt
- Output: prioritized, human-readable alert summaries with recommended actions

**Log Sources:**
| Source | VLAN | Log Type |
|--------|------|----------|
| OPNsense | All | Firewall / traffic logs |
| Windows 11 | 30 | Windows Event logs |
| Metasploitable | 30 | Syslog |
| Honeypot | 30 | Session / credential logs |

**Deliverables:**
- Wazuh deployment documentation (setup, agent configuration, data sources)
- AI alert summarizer tool (Python, GitHub-published)
- Sample Wazuh alert inputs and AI-generated summaries
- README with full architecture diagram of the ingestion pipeline

---

### Capstone Phase 3 — AI-Powered SOC Dashboard

**What it does:** Aggregates outputs from Phase 1 and Phase 2 into a web-based dashboard with real-time alert visualization, attack timeline, and an AI-generated executive summary panel.

**Why it matters:** Communication is as important as detection in security operations. A dashboard that translates raw security data into an executive-readable view demonstrates exactly the technical-to-management translation that MSISPM roles require.

**How to build it:**
- Python/Flask backend aggregating Phase 1 log analysis + Phase 2 SIEM alerts
- HTML/CSS frontend with live or near-live data refresh
- Attack timeline visualization (charting alert activity over time)
- AI-generated executive summary panel (daily/session summary in plain English)
- Severity breakdown panels (critical / high / medium / low)

**Technical Stack:**
- Python, Flask
- OpenAI API (and/or Ollama for local inference on Alienware RTX 4070)
- Wazuh REST API
- HTML/CSS/JavaScript for dashboard frontend
- Recharts or Chart.js for data visualization

**Deliverables:**
- Full dashboard codebase (GitHub-published)
- Live demo screenshots and/or recorded demo video
- Architecture diagram showing complete data pipeline (Phase 1 + 2 + 3)
- Master README tying all three capstone phases together as one narrative
- Executive summary document: written in plain English for a non-technical audience, demonstrating management communication skill
- Reflection writeup: design decisions, challenges encountered, lessons learned

### Capstone Success Criteria
The complete capstone should be demonstrable live in an interview — ideally showing real data flowing from the lab through the pipeline to the dashboard. The documentation should be thorough enough that a senior engineer could review it, and the executive summary should be clear enough that a non-technical hiring manager could understand what was built and why it matters.

---

## Timeline

> Note: Project work begins after the AI certification phase (~3 months of cert/learning prep). Estimated project timeline assumes approximately 3 hours/day of focused work.

| Week | Milestone |
|------|-----------|
| 1-2 | Complete Phase 1: Lab Documentation |
| 3-5 | Build Phase 2: AI Traffic Classifier |
| 6-7 | Deploy Phase 3: Honeypot |
| 8-9 | Capstone Phase 1: AI Log Analyzer |
| 10-11 | Capstone Phase 2: SIEM + AI Summarizer |
| 12 | Capstone Phase 3: SOC Dashboard + portfolio finalization |

---

## AI & Certification Foundation

These projects are supported by a parallel learning track completed before and during project work :

| Credential | Platform | Purpose |
|------------|----------|---------|
| Microsoft AI-900 | Microsoft Learn | AI fundamentals, responsible AI, Azure AI services |
| Google AI Essentials | Google | Practical AI tools, prompt engineering |
| CS50 AI with Python | Harvard (edX) | Search, probability, neural networks, ML classification |
| Harvard ML with Python | Harvard (edX) | ML pipeline, model evaluation — direct prep for Phase 2 |

The CS50 AI and Harvard ML courses are intentional preparation for the AI Traffic Classifier project — specifically the classification, model evaluation, and scikit-learn concepts covered in those courses.

---

## Tools & Technologies (Across All Projects)

| Category | Tools |
|----------|-------|
| Languages | Python, Bash, HTML/CSS |
| ML / AI | scikit-learn, pandas, matplotlib, OpenAI API, Ollama |
| Security | Kali Linux, Metasploit, Wireshark/tshark, Nmap, Wazuh |
| Infrastructure | OPNsense, Netgear switch, Cisco switch, UTM hypervisor |
| Development | Jupyter notebooks, Flask, Git/GitHub |
| Hardware | Alienware m16 R2 (Intel Ultra 9 185H, 64GB RAM, RTX 4070) |

---

## How to Use This Document

This roadmap is a living reference. As each project is completed:
- Update the status column in the Portfolio Overview table
- Link the completed GitHub repository in the relevant project section
- Add any lessons learned that affected subsequent project planning

Each project has its own dedicated GitHub repository and its own focused Claude thread for development support. This document lives in the lab documentation repo as the connective tissue between all of them.

---

*Last updated: May 2026*
*Author: Tyce Erickson*
*Program: CMU MSISPM, Fall 2026*
