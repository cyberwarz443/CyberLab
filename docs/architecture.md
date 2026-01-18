flowchart TB
  %% =========================
  %% External
  %% =========================
  RU[Remote User]:::ext
  Internet[(Internet)]:::ext

  %% =========================
  %% Edge
  %% =========================
  FW[Firewall / Edge Router\nUDM Pro (ref) / pfSense (alt)]:::edge

  Internet --> FW

  %% Note about UDM management limitations
  DEFVLAN[[Default / "Mgmt" VLAN\n(UniFi gear mgmt lives here;\navoid placing other services on this VLAN)]]:::vlan

  %% =========================
  %% VLANs (baseline)
  %% =========================
  subgraph VLANS[LAN Segmentation (Deny-by-Default Between VLANs)]
    direction TB

    subgraph GUEST[Guest VLAN]
      direction TB
      GuestClients[Guest Clients]:::client
    end

    subgraph IOT[IoT VLAN]
      direction TB
      IoTDevices[IoT Devices]:::client
    end

    subgraph TRUSTED[Trusted VLAN]
      direction TB
      Workstations[Trusted Endpoints\n(Users / Servers)]:::client

      %% Core services can live here in minimal deployments
      ZBX[Zabbix\n(Monitoring)]:::svc
      WAZ[Wazuh\n(SIEM: vuln + compliance + HIDS/FIM)]:::svc
      SYSLOG[Syslog Receiver]:::svc
      LOKI[Loki\n(Log Storage)]:::svc
      GRAF[Grafana\n(Dashboards)]:::svc
      PROM[Prometheus\n(Metrics TSDB)]:::svc
    end

    %% Optional VLAN: Security/Management
    subgraph SECMGMT[Optional: Security / Management VLAN]
      direction TB
      SecNote[[If you create this VLAN,\nplace security tooling here instead of Trusted]]:::note
    end
  end

  %% Firewall defines VLANs (logical)
  FW --- DEFVLAN
  FW --- TRUSTED
  FW --- IOT
  FW --- GUEST
  FW --- SECMGMT

  %% =========================
  %% Telemetry flows
  %% =========================

  %% Monitoring (metrics)
  Workstations -->|metrics / health| ZBX
  IoTDevices -->|availability checks\n(optional)| ZBX
  GuestClients -->|availability checks\n(optional)| ZBX

  %% Prometheus metrics (optional in diagram, but included per your note)
  Workstations -->|metrics| PROM
  PROM -->|dashboards| GRAF

  %% Central logging
  FW -->|firewall logs| SYSLOG
  Workstations -->|syslog / app logs| SYSLOG
  IoTDevices -->|syslog (if supported)| SYSLOG

  SYSLOG -->|log pipeline| LOKI
  LOKI -->|search & dashboards| GRAF

  %% SIEM (security telemetry)
  Workstations -->|agent telemetry\n(vuln, compliance,\nFIM, security events)| WAZ
  WAZ -->|alerts| NOTIFY[Notifications\nEmail / Chat / etc.]:::notify
  ZBX -->|alerts| NOTIFY

  %% =========================
  %% Remote Access (two patterns)
  %% =========================

  %% WireGuard pattern
  RU -->|VPN to public endpoint| FW
  FW -->|port allowed\n("hole" for VPN)| WG[WireGuard\n(VM or Container)]:::svc
  WG -->|access to internal resources| Workstations

  %% Twingate pattern (optional)
  RU -.->|Zero Trust| ZTB[ZT Broker\n(Twingate cloud)]:::ext
  ZTB -.-> ZTC[ZT Connector / Appliance\n(inside LAN)]:::svc
  ZTC -.->|resource access| Workstations

  %% =========================
  %% Styling
  %% =========================
  classDef ext fill:#f7f7f7,stroke:#555,stroke-width:1px
  classDef edge fill:#fff3cd,stroke:#856404,stroke-width:1px
  classDef vlan fill:#e8f4ff,stroke:#1f5f9f,stroke-width:1px
  classDef client fill:#ffffff,stroke:#666,stroke-width:1px
  classDef svc fill:#eaffea,stroke:#2f7d32,stroke-width:1px
  classDef notify fill:#ffe8f1,stroke:#a61b5b,stroke-width:1px
  classDef note fill:#f0f0f0,stroke:#888,stroke-dasharray: 4 4
