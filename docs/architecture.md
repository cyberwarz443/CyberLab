# Architecture overview

This document explains how to think about the system architecture before diving into implementation details.

The architecture is intentionally documented in layers so that each concern can be understood independently, while still fitting into a coherent whole.

If you are unsure where to start in this documentation set, start here.

---

## How this architecture is organized

The system is designed around a small number of core ideas:

- **Strong network segmentation**
- **Clear separation of monitoring, logging, and security**
- **Operational realism (SOC-of-one)**
- **Repeatability and evidence**

Each of the documents in this directory expands on one of these ideas.

---

## 1. Network trust boundaries

The foundation of the architecture is network segmentation.

The environment is divided into multiple VLANs with deny-by-default rules enforced at the firewall.  
This limits blast radius and improves signal quality for monitoring and security tooling.

Defined trust zones include:

- Trusted VLAN
- IoT VLAN
- Guest VLAN
- Optional Security/Management VLAN

This layer answers the question:

> *“What is allowed to talk to what, and why?”*

For details, see:  
- [Network trust boundaries](network-trust-boundaries.md)

---

## 2. Telemetry and security data flows

Once trust boundaries are defined, the next concern is **visibility**.

Monitoring, centralized logging, and SIEM functions are intentionally separated:

- **Monitoring** focuses on availability and performance
- **Logging** provides raw event evidence
- **SIEM** provides security posture, vulnerability detection, and compliance signals

This separation avoids overloading any single tool and keeps alerts actionable.

This layer answers the question:

> *“How do I know what is happening in the environment?”*

For details, see:  
- [Telemetry and security data flows](telemetry-security-data-flows.md)

---

## 3. Reference deployment

The architecture is grounded in a real, working environment.

The reference deployment documents:

- hardware and virtualization choices
- network layout
- telemetry stack composition
- patching and remote access decisions

This layer answers the question:

> *“What does this look like when actually implemented?”*

For details, see:  
- [Reference deployment](reference-deployment.md)

---

## 4. Operational workflow

Architecture is only useful if it can be operated.

The SOC-of-one workflow shows how alerts, logs, and patching are used together during daily operations and incidents.

This layer answers the question:

> *“What do I actually do when something happens?”*

For details, see:  
- [SOC-of-one workflow](soc-workflow.md)

---

## How to use this documentation

You do not need to read every document to benefit from this project.

- Start here to understand the system design
- Read deeper based on your interests or needs
- Treat the reference deployment as one proven example, not the only correct approach
