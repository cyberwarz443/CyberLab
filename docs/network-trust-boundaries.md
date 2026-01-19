# Network trust boundaries

This document describes how the network is segmented and how trust boundaries are enforced.

The goal is to limit blast radius, reduce noise, and ensure that security tooling and sensitive systems are isolated from untrusted devices.

---

## Trust boundary diagram

```mermaid
flowchart TB
  Internet[(Internet)]
  RU[Remote User]

  FW[Firewall / Edge\nUDM Pro or pfSense]

  Internet --> FW
  RU -->|VPN or Zero Trust| FW

  subgraph LAN[Internal Network]
    Trusted[Trusted VLAN\nUsers and Servers]
    IoT[IoT VLAN]
    Guest[Guest VLAN]

    FW --> Trusted
    FW --> IoT
    FW --> Guest
  end

  subgraph Optional[Optional Security or Management VLAN]
    SecNote[Security Tooling\nOptional Placement]
  end

  Trusted -.-> SecNote
