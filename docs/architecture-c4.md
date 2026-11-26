# C4 Architecture – Infotainment Service Layer

## 1. Context Diagram (Level 0)

```mermaid
flowchart LR
    %% external actors
    actor "Vehicle Bus" as V
    actor "Mobile Application" as M
    actor "Cloud Backend" as C

    %% system under design
    system "Infotainment Service Layer" as S

    %% relationships
    V --> S : CAN / LVDS messages
    M --> S : REST / WebSocket commands
    S --> C : Telemetry / OTA updates
```

*Description:* The Infotainment Service Layer sits between the vehicle bus, mobile apps and a cloud backend. It receives sensor and control messages from the bus, exposes a command API to mobile applications, and sends telemetry / OTA data to the cloud.

## 2. Container Diagram (Level 1)

```mermaid
flowchart TB
    %% boundary of the system
    subgraph "Infotainment Service Layer"
        direction TB

        %% core modules
        module "Service Core" as SC
        module "Communication Adapter" as CA
        module "Data Persistence" as DP
        module "Configuration Manager" as CM
        module "Telemetry Publisher" as TP
    end

    %% external interfaces
    actor "Vehicle Bus" as V
    actor "Mobile App" as MA
    actor "Cloud Backend" as CB

    %% connections
    V --> CA : CAN / LVDS inbound
    MA --> CA : REST / WebSocket inbound
    CA --> SC : parsed messages
    SC --> DP : store / retrieve records
    SC --> CM : load / save settings
    SC --> TP : publish telemetry
    TP --> CB : HTTP / MQTT outbound
```

*Description:* The service layer is decomposed into five logical containers that each own a set of responsibilities:
- **Service Core** – orchestration, state machine, and lifecycle management.
- **Communication Adapter** – abstraction over vehicle bus (CAN/LVDS) and external connectivity (REST, WebSocket).
- **Data Persistence** – local storage (SQLite, flash) of mappings, preferences, and cached data.
- **Configuration Manager** – loading, validating, and distributing configuration files (JSON, XML).
- **Telemetry Publisher** – formatting and sending telemetry to the cloud backend.

All containers communicate through well‑defined interfaces (C++ classes / ROS‑like topics) which are illustrated by the arrows.

---
*Diagrams are written in Mermaid syntax and render correctly in GitHub, VS Code, GitLab and other Mermaid‑aware Markdown renderers.*