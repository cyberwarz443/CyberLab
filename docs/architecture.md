# Architecture overview

This document provides an overview of the system architecture and how the major components interact.

The architecture is intentionally documented in layers to keep each concern clear and maintainable.

---

## Network trust boundaries

The environment is segmented into multiple VLANs with deny-by-default rules enforced at the firewall.

- Trusted VLAN
- IoT VLAN
- Guest VLAN
- Optional Security/Management VLAN

For details, see:
- [Network trust boundaries](network-trust-boundaries.md)

---

## Telemetry and security data flows

Monitoring, logging, and SIEM functions are intentionally separated.

- Monitoring focuses on availability and performance
- Logging provides raw event evidence
- SIEM provides security posture, vulnerability detection, and compliance

For details, see:
- [Telemetry and security data flows](telemetry-security-data-flows.md)

---

## Reference deployment

A working reference build is documented to ground the architecture in reality.

- Hardware
- Hypervisor
- VLAN layout
- Tooling choices

See:
- [Reference deployment](reference-deployment.md)
