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
