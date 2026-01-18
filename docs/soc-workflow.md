# SOC-of-one workflow

This document describes how monitoring, centralized logging, SIEM, and patching are used together in day-to-day operations and during incidents.

This workflow reflects a **SOC-of-one** reality:
- limited time
- imperfect signals
- contextual decision-making
- prioritization over theoretical completeness

---

## Alert sources and intent

This environment uses multiple systems that may all generate alerts, but they serve different purposes.

- **Zabbix**: operational health and availability
- **Wazuh**: security posture, vulnerability awareness, and exposure
- **Central logging**: evidence and root cause analysis

Alerts are interpreted based on **context**, not in isolation.

---

## Zabbix alerts (operational)

Zabbix alerts are evaluated based on:
- the system involved
- whether a change was expected
- the role of the system

### Common scenarios

#### Expected events (low priority)
- Reboots initiated by the operator
- Planned maintenance
- Known configuration changes

These alerts are generally acknowledged and ignored.

#### Unexpected events (investigate)
- System reboot with no known change
- Service restart without explanation
- Host unreachable unexpectedly

These trigger a **“what happened?”** mindset.

#### Immediate response events
- Disk full
- Critical service not responding
- Network-facing service unavailable

Response urgency depends on whether the affected system is:
- internet-facing
- providing critical access (e.g., VPN)
- depended on by other systems

---

## Wazuh alerts (security)

Wazuh alerts are often noisier than operational alerts and are interpreted differently.

### Vulnerability alerts

- Vulnerability findings are reviewed but not treated as immediate incidents
- Priority is based on **exposure**, not just severity score

Highest priority systems:
- Firewall
- WireGuard LXC / VM
- Any system directly exposed to the internet

Lower priority systems:
- Internal-only systems
- Lab or non-critical hosts

#### Known false positives
Some vulnerabilities are flagged based on package versions even when the affected service is not in use.

Example:
- WireGuard host flagged for a vulnerability in a service it does not expose

These findings are documented mentally and deprioritized unless exposure changes.

---

### Behavioral and integrity alerts

These are treated more seriously than vulnerability findings, especially when they involve:
- authentication events
- file integrity changes
- unexpected process behavior

Context is always considered before escalation.

---

## First-response workflow

### When a Zabbix alert fires

1. **Confirm context**
   - Did Ansible or maintenance recently run?
   - Was a reboot or change expected?

2. **Check system state**
   - Is the system actually down or degraded?
   - Is it self-recovering?

3. **Consult logs if needed**
   - System logs
   - Firewall logs (if network-related)

Understanding the environment and recent activity is critical before assuming failure.

---

### When a Wazuh alert fires

1. **Classify the alert**
   - Vulnerability
   - Configuration drift
   - Behavioral / security event

2. **Assess exposure**
   - Is the system internet-facing?
   - Does it provide critical access?

3. **Decide on action**
   - Immediate remediation
   - Scheduled patching
   - Acceptance of risk (with awareness)

Compliance frameworks (e.g., NIST) are **not** driving decisions in this environment.

---

## Evidence gathering and investigation

When something does not behave as expected, evidence is gathered from multiple sources.

### Example scenario: system cannot communicate with another system

Steps:
1. Check firewall logs to confirm whether traffic is being blocked
2. Validate VLAN placement and inter-VLAN rules
3. Review system logs on both ends
4. Confirm no recent changes introduced unintended restrictions

Understanding **data flow paths** is essential to effective troubleshooting.

---

## Remediation and patching

### Current state

- **WireGuard** is patched weekly using:
  - `apt update`
  - `apt upgrade`
- Other systems are patched less frequently (e.g., monthly) due to:
  - internal-only exposure
  - lower risk profile

### Future state

- Ansible will be used to standardize:
  - patching
  - baseline configuration
  - repeatable remediation

---

## Verification after remediation

After patching or remediation:

- Confirm services are running as expected
- Verify the issue no longer appears in:
  - Zabbix
  - Wazuh
  - central logs
- Ensure no new alerts were introduced as a side effect

Remediation is not considered complete until verification is done.

---

## Alert fatigue and noise management

Alert fatigue is actively managed.

### Strategies used

- New systems are not immediately configured to send email or external alerts
- Systems are allowed to “bake in” to establish a baseline
- Alerts are tuned before escalation channels are enabled

This approach:
- reduces noise
- improves signal quality
- prevents ignoring meaningful alerts

---

## What is normal vs what is concerning

Some behavior may look alarming at first glance but is normal in context.

Examples of normal behavior:
- brief outages during patch windows
- expected reboots
- vulnerability alerts for unused services

Examples of concerning behavior:
- unexplained reboots
- repeated authentication failures on exposed systems
- integrity changes on internet-facing hosts

Context and familiarity with the environment matter more than raw alert volume.

---

## Key takeaway

This SOC-of-one workflow prioritizes:
- understanding the environment
- contextual decision-making
- practical risk reduction

The goal is not to chase every alert, but to **respond effectively to the ones that matter**.
