## Reference deployment (summary)

This repository reflects a working reference build:

- Hypervisor: Proxmox
- Firewall: UDM Pro (pfSense also applicable)
- VLANs: Trusted, IoT, Guest
- Monitoring: Zabbix
- SIEM: Wazuh
- Central logging: Syslog + Loki + Grafana
- Remote access: WireGuard (VPN) or Twingate (Zero Trust)
- Hardware: 32GB RAM, Intel i7-class CPU
