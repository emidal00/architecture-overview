# Architecture Overview

This document provides a high-level architecture overview for the platform depicted in the attached system diagram.

```mermaid
flowchart TB
    classDef edge fill:#eaf2ff,stroke:#4a6fa5,stroke-width:1px;
    classDef primary fill:#dfeeff,stroke:#2e6bb0,stroke-width:1.5px;
    classDef warn fill:#fff1cc,stroke:#d39c00,stroke-width:1.5px;
    classDef dark fill:#dfe8f5,stroke:#2b4d6f,stroke-width:1.5px;
    classDef app fill:#dff6f0,stroke:#1b7f5a,stroke-width:1.5px;
    classDef infra fill:#f3e5ff,stroke:#6d4fb8,stroke-width:1.5px;

    subgraph Ext["External Services"]
        DNS["External DNS\napi.example.com"]
    end

    Client["Client / User"]

    subgraph Global["Global Routing & Availability"]
        GTM["GTM / Global Traffic\nManager"]
        P1["Arbank Primary"]
        P2["Arbank Backup"]
    end

    subgraph Edge["Ingress & Security"]
        FW1["Firewall\nHARRIS-PRI"]
        FW2["Firewall\nHARRIS-SEC"]
        IPS1["IPS-Primary"]
        IPS2["IPS-Secondary"]
    end

    subgraph DC1["Primary Data Center"]
        F5P["F5 LTM + ASM\nPrimary"]
        API1["API Gateway\n/ Service Layer"]
        APP1["Application Services"]
    end

    subgraph DC2["Secondary Data Center"]
        F5S["F5 LTM + ASM\nSecondary"]
        API2["API Gateway\n/ Service Layer"]
        APP2["Application Services"]
    end

    subgraph Platform["Platform Runtime"]
        AN1["Anthos Cluster\nPrimary"]
        AN2["Anthos Cluster\nSecondary"]
        MS["Anthos Management\nand Service Mesh"]
    end

    Client --> DNS
    DNS --> GTM
    GTM --> P1
    GTM --> P2

    P1 --> FW1
    P2 --> FW2
    FW1 --> IPS1
    FW2 --> IPS2

    IPS1 --> F5P
    IPS2 --> F5S

    F5P --> API1
    F5S --> API2

    API1 --> APP1
    API2 --> APP2

    APP1 --> AN1
    APP2 --> AN2
    AN1 --> MS
    AN2 --> MS

    class Client,DNS,GTM,P1,P2 primary;
    class FW1,FW2,IPS1,IPS2 warn;
    class F5P,F5S,API1,API2 dark;
    class APP1,APP2,AN1,AN2,MS app;
    class Ext,Global,Edge,DC1,DC2,Platform infra;
```

## Notes

- The diagram illustrates a dual-site, highly available deployment pattern.
- Traffic enters through external DNS and global traffic management.
- Security enforcement is applied at the firewall and IPS layers before load balancing.
- F5 LTM + ASM acts as the main ingress and application security control point.
- Application services run on Anthos clusters in primary and secondary data centers.
- Anthos management provides orchestration and service-level consistency across sites.
