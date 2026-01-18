# Documentation guide

This directory contains the detailed documentation for this project.

If you are new to the repository, follow the documents in the order below.  
Each document builds on the previous one.

---

## Suggested reading order

### 1. Architecture overview
**[architecture.md](architecture.md)**

Start here to understand:
- the overall design
- how the components fit together
- how the documentation is structured

---

### 2. Network trust boundaries
**[network-trust-boundaries.md](network-trust-boundaries.md)**

Explains:
- VLAN segmentation
- firewall philosophy
- trust boundaries and isolation

---

### 3. Telemetry and security data flows
**[telemetry-security-data-flows.md](telemetry-security-data-flows.md)**

Explains:
- monitoring vs logging vs SIEM
- how data moves through the environment
- why tools are separated by function

---

### 4. Reference deployment
**[reference-deployment.md](reference-deployment.md)**

Documents:
- the actual hardware and hypervisor used
- VM vs container decisions
- logging stack composition
- patching and remote access

---

### 5. SOC-of-one workflow
**[soc-workflow.md](soc-workflow.md)**

Shows:
- how alerts are triaged
- how investigations are performed
- how patching and verification fit into operations

---

## How to use this documentation

- You do not need to read everything to benefit from this project
- Start with the sections most relevant to your use case
- The reference deployment and SOC workflow reflect one working implementation, not the only correct approach
