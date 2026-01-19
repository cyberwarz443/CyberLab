# Telemetry and security data flows

This document describes how monitoring, centralized logging, and SIEM data flows through the environment.

The intent is to separate concerns:

- **Monitoring (Zabbix)** answers: *Is it healthy / up / degrading?*
- **Central logging (Syslog -> Loki -> Grafana)** answers: *What happened?*
- **SIEM (Wazuh)** answers: *Is it vulnerable, non-compliant, or suspicious?*

---

## Data flow diagram

```mermaid
flowchart LR
  Trusted[Trusted VLAN\nEndpoints and Servers]
  FW[Firewall / Edge]

  Zabbix[Zabbix\nMonitoring]
  Logging[Central Logging\nSyslog -> Loki -> Grafana]
  Wazuh[Wazuh\nSIEM]
  Notify[Notifications\nEmail / Chat / etc.]

  %% Collection flows
  Trusted -->|metrics| Zabbix
  Trusted -->|logs| Logging
  Trusted -->|security telemetry| Wazuh
  FW -->|firewall logs| Logging

  %% Alert flows
  Zabbix -->|alerts| Notify
  Wazuh -->|security alerts| Notify
  
  .
