flowchart LR
  Trusted[Trusted VLAN\nEndpoints and Servers]
  FW[Firewall / Edge]

  Zabbix[Zabbix\nMonitoring]
  Logging[Central Logging\nSyslog -> Loki -> Grafana]
  Wazuh[Wazuh\nSIEM]

  Trusted -->|metrics| Zabbix
  Trusted -->|logs| Logging
  Trusted -->|security telemetry| Wazuh

  FW -->|firewall logs| Logging

  Zabbix -->|alerts| Notify[Notifications]
  Wazuh -->|security alerts| Notify
