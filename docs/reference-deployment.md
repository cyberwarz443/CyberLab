# Reference deployment

This document describes the reference environment used for this project.  
It reflects a working deployment and is intended to ground the architecture and documentation in a real, repeatable setup.

---

## Hardware

- **CPU:** Intel i7-class (example: i7-6700T)
- **RAM:** 32 GB
- **Storage:** SSD / NVMe for log-heavy workloads
- **NICs:** Single NIC (VLANs handled at the firewall)

This hardware profile is sufficient to run monitoring, centralized logging, and SIEM components concurrently in a small environment.

---

## Virtualization

- **Hypervisor:** Proxmox (reference)
- **VM vs container philosophy:**
  - Core infrastructure and security services run in **VMs** where isolation is preferred
  - Supporting services (logging stack components) run as **containers** to reduce overhead and simplify upgrades
  - Containers are orchestrated via Docker / Docker Compose (no Kubernetes required)

---

## Network

- **VLANs:**
  - Trusted
  - IoT
  - Guest
- **Firewall role:**
  - VLAN termination
  - Inter-VLAN routing with deny-by-default rules
  - Source of firewall and network logs
- **DNS placement:**
  - DNS resolvers placed within the VLANs they serve
  - Firewall rules explicitly permit DNS traffic as required

---

## Telemetry stack

The telemetry stack is intentionally split into **three distinct layers**: monitoring, centralized logging, and SIEM.  
Although all three may process “logs” in some form, their purposes are different.
---

## Configuration management and patching

**Tool:** Ansible

Ansible is used for:
- operating system patching
- baseline configuration enforcement
- repeatable system changes across the environment

Patching is automated via Ansible playbooks and scheduled execution, rather than ad-hoc package updates.

Evidence of patching includes:
- local logs on the target systems
- centralized logs in the logging stack
- notifications indicating successful or failed runs

---

### Monitoring (Zabbix)

**Role:** operational visibility and health monitoring

- Tracks availability and performance of:
  - hosts
  - services
  - storage
  - network devices
- Generates alerts when systems are down or degrading
- Not used for security detection or compliance

Zabbix answers:  
**“Is something broken or unhealthy right now?”**

---

### Central logging (Syslog + Loki + Grafana)

**Role:** raw event collection, retention, and investigation

This reference deployment uses a **containerized logging stack**, composed of:

- **Syslog receiver**
  - Ingests logs from:
    - firewall / edge devices
    - Linux systems
    - NAS devices (where supported)
- **Promtail**
  - Collects and forwards logs into Loki
- **Loki**
  - Stores and indexes log data
  - Optimized for log aggregation rather than full-text SIEM analysis
- **Grafana**
  - Provides dashboards and ad-hoc log search
  - Used for troubleshooting and evidence during incidents

Example container images used in the reference deployment:

- `grafana`
- `loki`
- `promtail`

This stack answers:  
**“What exactly happened on this system or network device?”**

Central logging is used for:
- root cause analysis
- forensic review
- validation of patching and configuration changes
- supporting investigations initiated by monitoring or SIEM alerts

---

### SIEM (Wazuh)

**Role:** security posture, vulnerability management, and compliance

- Collects agent-based telemetry from hosts
- Performs:
  - vulnerability detection
  - file integrity monitoring
  - configuration and policy compliance checks
  - security-focused event correlation
- Generates security alerts distinct from operational alerts

While Wazuh ingests logs, it is **not used as the primary log storage system**.  
Raw logs remain the responsibility of the centralized logging stack.

Wazuh answers:  
**“Is this system vulnerable, non-compliant, or behaving suspiciously?”**

---

## Remote access

- **VPN option:** WireGuard
  - Key-based authentication
  - Self-hosted
  - Full network access based on firewall policy
- **Zero Trust option:** Twingate
  - Resource-level access
  - Identity-centric controls
  - Introduces a third party into the data path

Remote access is required to reach management and trusted resources without exposing administrative services to the public internet.

---

## Lessons learned

- Separating monitoring, logging, and SIEM reduces noise and confusion
- Containerizing the logging stack simplifies upgrades and maintenance
- VLAN segmentation significantly improves signal quality in logs and alerts
- Avoid overloading a single tool to perform multiple roles
