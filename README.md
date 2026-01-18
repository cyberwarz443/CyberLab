# SMB-Minimal Cybersecurity Stack  
### Home / Homelab / Small Business (Reference Build)

This repository documents a **practical, SMB-minimal cybersecurity stack** built using primarily open-source tooling and commodity hardware.

It is based on a real-world reference implementation and is suitable for:

- home networks with elevated security requirements  
- homelabs  
- small environments handling **sensitive or business-critical data**

The objective is not enterprise perfection — it is **visibility, correlation, evidence, and secure access** implemented in a way that is realistic for a small environment or a “SOC of one”.

---

## Primary security outcomes

1. **Detect, correlate, and investigate security events (SIEM)**
   - Identify suspicious, vulnerable, or non-compliant behavior
   - Correlate events across hosts, firewall, DNS, and storage
   - Track vulnerabilities, configuration drift, and policy violations
   - Support investigations and incident response

2. **Operational visibility and reliability (Monitoring)**
   - Detect outages and degraded performance
   - Alert when systems, services, or resources are unhealthy
   - Provide historical metrics for trend and capacity analysis

3. **Centralized logging and evidence**
   - Retain logs for troubleshooting, forensics, and proof
   - Support investigations initiated by monitoring or SIEM alerts
   - Maintain an immutable operational record

4. **Automated patching with proof**
   - Systems update automatically
   - Confirmation is generated and retained
   - Evidence exists locally and centrally for audit or compliance needs

5. **Secure remote access**
   - No exposed administrative ports
   - Strong authentication
   - Clear trust boundaries

---

## Target audience

- Home users who care about security
- Homelab builders
- IT generalists
- **IT security professionals or those entering the field**

Assumed skill level: **intermediate**

---

## Platform & virtualization

### Hypervisors

- **Proxmox** (recommended): bare-metal, efficient, strong snapshot and backup support  
- **VMware**: viable if already in use, though free options have become limited  
- **Hyper-V**: functional, but commonly runs on a general-purpose Windows host, which consumes resources and increases complexity

### Containers

- Fully supported and encouraged where appropriate  
- May be mixed with VMs depending on isolation and operational preference

---

## Hardware baseline (reference)

- **RAM:** 32 GB (practical minimum for monitoring, logging, SIEM, and remote access)
- **CPU:** anything from the last ~5 years is generally sufficient  
  - Reference system: Intel i7-6700T
- **Storage:** SSD / NVMe strongly recommended for log-heavy workloads

---

## Network segmentation (minimum viable)

### VLANs

- **Trusted** – workstations, laptops  
- **IoT** – cameras, TVs, smart devices  
- **Guest** – untrusted client access  

### Firewall philosophy

- **Deny by default** between VLANs  
- Explicitly allow only what is required  
- DNS resolvers should reside in the VLANs they serve or be tightly restricted via firewall rules  

---

## Firewall / edge considerations

Viable options include:

- UDM-class appliances  
- OPNsense / pfSense systems  

Important considerations:

- Ensure **at least two Ethernet ports**
- Size hardware for **stateful filtering plus IDS/IPS**
- IDS/IPS can **significantly degrade performance** on underpowered systems

---

## DNS security & filtering

### NextDNS
**Pros**
- Profile-based policies
- Roaming device support

**Cons**
- Cloud dependency
- Third party in the data path

### Pi-hole / AdGuard Home
**Pros**
- Local control
- Open source

**Cons**
- Per-VLAN policies may require multiple instances

---

## Telemetry stack overview

This environment intentionally separates **monitoring**, **logging**, and **SIEM** responsibilities.

### Monitoring — Zabbix
- Availability and performance
- Fast operational alerting
- Metrics and trends

### Centralized logging — Syslog + Grafana + Loki
- Raw event storage and search
- Root cause analysis
- Incident evidence

### SIEM — Wazuh
- Vulnerability management
- Host-based intrusion detection
- File integrity monitoring
- Compliance and configuration drift
- Security-focused correlation and alerting

---

## Monitoring vs Logging vs SIEM

| Capability | Zabbix | Loki Stack | Wazuh |
|---------|--------|------------|-------|
| Health checks | ✅ | ❌ | ❌ |
| Metrics | ✅ | ❌ | ❌ |
| Raw logs | ❌ | ✅ | ⚠️ |
| Vulnerability detection | ❌ | ❌ | ✅ |
| Compliance | ❌ | ❌ | ✅ |
| Security correlation | ❌ | ❌ | ✅ |

---

## Central logging requirements

Log anything that can reach — or be reached from — the internet:

- Firewall / edge
- Internet-accessible servers
- NAS authentication and file activity
- Windows Event Logs (only if internet-accessible)

---

## Automated patching

- **Windows:** largely automatic, still needs visibility
- **Linux:** explicit automation required

### Proof requirements
A patch cycle is complete only when:
1. Local log exists  
2. Central log / SIEM record exists  
3. Notification is sent  

---

## Secure remote access

### Zero trust access (e.g., Twingate)
- Fine-grained access
- Third party in the data path
- Can interfere with CarPlay / Android Auto

### Traditional VPN (e.g., WireGuard)
- Fully self-hosted
- Key-based authentication
- Broader network access

Admin interfaces must never be exposed publicly.

---

## What “good” looks like

- Zabbix alerts → logs + Wazuh explain why  
- Vulnerabilities detected → patched → evidence retained  
- Remote access works → admin surfaces stay private  

---

## High-level architecture
flowchart TB
  Internet[(Internet)]
  FW[Firewall / Edge\nUDM Pro or pfSense]

  subgraph LAN[Internal Network]
    Trusted[Trusted VLAN\n(Users & Servers)]
    IoT[IoT VLAN]
    Guest[Guest VLAN]

    Zabbix[Zabbix\nMonitoring]
    Logging[Central Logging\n(Syslog + Loki + Grafana)]
    Wazuh[Wazuh\nSIEM]
  end

  Remote[Remote User]

  Internet --> FW
  FW --> Trusted
  FW --> IoT
  FW --> Guest

  Trusted --> Zabbix
  Trusted --> Logging
  Trusted --> Wazuh

  FW --> Logging

  Remote -->|VPN or Zero Trust| FW

This high-level diagram shows the separation of concerns:
- **Zabbix** provides operational monitoring and health checks
- **Central logging** (Syslog + Loki + Grafana) provides raw event evidence
- **Wazuh** provides SIEM capabilities such as vulnerability detection, compliance, and security correlation

These services may reside on the Trusted VLAN in minimal deployments, or on a dedicated Security/Management VLAN in more advanced setups.


---

## Disclaimer

This project is for defensive security and operational reliability.  
You are responsible for proper deployment and compliance.
