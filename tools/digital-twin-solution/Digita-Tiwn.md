# AENAON Digital Twin — Technical Documentation

Reference documentation for the AENAON wind-farm digital twin: an OPC UA based OT/ICS simulator with a web HMI/SCADA dashboard. It describes the architecture, every component, the data flow, the security model, configuration and how to extend the project.

> **Scope note.** The environment is intentionally vulnerable and is meant to be attacked in a controlled range. This document describes how the system is built and how it behaves. It does not catalogue the deliberate weaknesses or list passwords. Default credentials live in `docker-compose.yml`, and secrets should be overridden through an `.env` file (see [Configuration](#11-configuration)).

---

## Table of contents

1. [Overview](#1-overview)
2. [Potential Open Call use](#2-potential-open-call-use)
3. [Access and deployment](#3-access-and-deployment)
4. [Getting started](#4-getting-started)
5. [Inputs and outputs](#5-inputs-and-outputs)
6. [Interfaces and dependencies](#6-interfaces-and-dependencies)
7. [Architecture](#7-architecture)
8. [Components](#8-components)
   - [8.1 Turbine simulator](#81-turbine-simulator)
   - [8.2 Avation lamp controller](#82-avation-lamp-controller)
   - [8.3 Ground Controller](#83-ground-controller)
   - [8.4 HMI / SCADA](#84-hmi--scada)
9. [Data flow and timing](#9-data-flow-and-timing)
10. [Security model](#10-security-model)
11. [Configuration](#11-configuration)
12. [Running the project](#12-running-the-project)
13. [OPC UA address space reference](#13-opc-ua-address-space-reference)
14. [HTTP reference](#14-http-reference)
15. [Database](#15-database)
16. [Extending the project](#16-extending-the-project)
17. [Troubleshooting](#17-troubleshooting)

---

## 1. Overview

**Provider:** ZELUS

**Service family:** Digital Twins & Virtualisation Services

A distributed simulation and digital twin environment for offshore wind farms. It uses OPC UA and TCP fieldbus communication to represent turbine operations, electrical infrastructure and environmental conditions.

Technical reference: *AENAON Open Call #1 Technical Annex*, v1.0 (7 August 2026).

The implementation in this repository models a small offshore wind farm and the control chain above it:

| Layer | Role | Technology |
|---|---|---|
| Field devices | Turbines (physics simulation) and aviation warning lamps | OPC UA servers (`asyncua`) |
| Aggregation | Ground Controller polls every field device and publishes one combined OPC UA server | OPC UA client + server |
| Supervision | HMI/SCADA web application with login, live dashboard and command console | FastAPI, Jinja2, SQLite, SSE |

Everything runs as eight Docker containers on two private bridge networks. Only the HMI web port and the Ground Controller OPC UA port are published to the host.

**Technology versions**

| Item | Version |
|---|---|
| Container base image | `python:3.12-slim` |
| OPC UA library | `asyncua==1.1.5` |
| Host Python for local scripts | 3.13 (3.14 breaks `asyncua` 1.1.5 with an `issubclass()` `TypeError`) |

---

## 2. Potential Open Call use

Experiment with simulated operational scenarios, monitoring and anomaly detection; examine predictive-maintenance approaches and industrial IoT integration within an agreed representative environment.

---

## 3. Access and deployment

The service is described as Docker-based and may be hosted on-premises or in a private cloud. ZELUS provides access details during onboarding and confirms the authorised deployment resources. Container images and deployment packages must not be assumed to be publicly downloadable.

---

## 4. Getting started

Agree the simulation objective and permitted infrastructure representation with ZELUS. Prepare relevant turbine parameters, ICS configurations and approved measurements. Confirm host prerequisites, resource requirements, approved deployment resources or hosted environment, dashboard access and applicable interface instructions with ZELUS before deployment.

Once the deployment resources are confirmed, see [Running the project](#12-running-the-project) for the steps and [Configuration](#11-configuration) for the settings.

---

## 5. Inputs and outputs

- **Inputs:** Turbine parameters, ICS configurations, sensor and weather measurements, and historical operational data, where available and authorised.
- **Outputs:** Simulated system states, performance metrics, anomaly results and reports.

In this implementation, the inputs are the turbine catalogue ([`common/turbines.json`](common/turbines.json)) and the environment settings ([Configuration](#11-configuration)). The outputs are the live dashboard, the `/get-current-metrics` JSON, the OPC UA values on the Ground Controller, and the stored metrics and command log ([Database](#15-database)).

---

## 6. Interfaces and dependencies

Known interfaces include web dashboards, OPC UA and REST APIs, alongside TCP fieldbus communication in the simulated environment. The Digital Twin supports the Cyber Range Solution. ZELUS agrees environment configuration and actual interface access; no beneficiary endpoints or deployment specification are supplied here.

This repository implements the web dashboard and its HTTP/JSON endpoints ([HTTP reference](#14-http-reference)) and OPC UA ([OPC UA reference](#13-opc-ua-address-space-reference)). The TCP fieldbus communication named in the technical annex is not part of this stack.

---

## 7. Architecture

```
 Browser ──HTTP :5001──►┌──────────────────────┐
                        │ hmi  (FastAPI)       │ 10.10.1.2
                        └──────────┬───────────┘
        challenge_net              │ OPC UA :4880 (login: hmi)
        10.10.1.0/24               │
 OPC UA clients ────────────────►┌─▼────────────────────┐
   (host :4880)                  │ ground_controller    │ 10.10.1.3 | 10.10.2.3
                                 └─┬──────────────────┬─┘
        field_net                  │ OPC UA           │ OPC UA
        10.10.2.0/24     :4860-4862│ (login: gc)      │ :4840 (login: gc)
                    ┌──────────────▼───┐        ┌─────▼─────────────┐
                    │ turbine_1..3     │        │ avation_1..3      │
                    │ 10.10.2.4–6      │        │ 10.10.2.7–9       │
                    └──────────────────┘        └───────────────────┘
```

### 7.1 Containers

| Container | Image source | Network address(es) | Ports |
|---|---|---|---|
| `hmi` | `SubStation/HMI/Dockerfile` | `challenge_net` 10.10.1.2 | container `5000`, published `5001` |
| `ground_controller` | `Controllers/Ground_Controller/Dockerfile` | `challenge_net` 10.10.1.3, `field_net` 10.10.2.3 | `4880`, published `4880` |
| `turbine_1` | `Turbines/Dockerfile` | `field_net` 10.10.2.4 | `4860` |
| `turbine_2` | `Turbines/Dockerfile` | `field_net` 10.10.2.5 | `4861` |
| `turbine_3` | `Turbines/Dockerfile` | `field_net` 10.10.2.6 | `4862` |
| `avation_1` | `Controllers/Avation/Dockerfile` | `field_net` 10.10.2.7 | `4840` |
| `avation_2` | `Controllers/Avation/Dockerfile` | `field_net` 10.10.2.8 | `4840` |
| `avation_3` | `Controllers/Avation/Dockerfile` | `field_net` 10.10.2.9 | `4840` |

Compose project name: `twin`. Container names are fixed short names (`turbine_1`, `hmi`, …), which are also the DNS names used for service discovery inside the networks.

### 7.2 Networks

| Network | Subnet | Members | Purpose |
|---|---|---|---|
| `twin_challenge_net` | 10.10.1.0/24 | `hmi`, `ground_controller` | The exposed, attacker-facing side |
| `twin_field_net` | 10.10.2.0/24 | turbines, lamp controllers, `ground_controller` | Private field network |

- The two bridge networks do not route to each other, so nothing on `challenge_net` can open a connection to a field device directly.
- The Ground Controller is the only container attached to both. It is the single bridge between the supervision side and the field side.
- `field_net` is not marked `internal`, so its containers can still reach the internet through the Docker host.

---

## 8. Components

### 8.1 Turbine simulator

File: [Turbines/Turbine_Simulator.py](Turbines/Turbine_Simulator.py)

One container per turbine. The turbine ID (`TURBINE_ID`, e.g. `TURB002`) determines the port: `4860 + n − 1`. Endpoint: `opc.tcp://0.0.0.0:<port>/turbine/`, namespace `urn:aenaon:turbine`, root object `Turbine_<ID>`.

**Physics model** (updated every 2 s):

| Parameter | Value |
|---|---|
| Cut-in wind speed | 3 m/s |
| Rated wind speed | 12 m/s |
| Cut-out wind speed | 22 m/s |
| Rated power | 3000 kW |
| Maximum rotor speed | 17 RPM |

- **Wind:** a mean-reverting random walk around 9 m/s, clamped to 0–25 m/s.
- **Operating band:** between cut-in and cut-out the turbine is `RUNNING`. RPM rises linearly from 5 to 17 between cut-in and rated, power follows a cubic curve up to rated and is flat above it, and pitch opens above rated wind speed to shed load.
- **Outside the band, or when stopped:** `Status` is `OFFLINE`, RPM and power are 0, and the blades are feathered (pitch 90°).
- **Temperature:** drifts from about 25 °C idle to about 45 °C at full load.
- **Generator load:** power as a percentage of rated power, 0 when offline.

**Variables** (all nodes are writable, but all except `Command` are overwritten on every 2-second tick):

| Variable | Type | Meaning |
|---|---|---|
| `RPM` | Double | Rotor speed |
| `Temperature_C` | Double | Nacelle temperature |
| `PowerOutput_kW` | Double | Active power |
| `WindSpeed` | Double | Wind speed in m/s |
| `PitchAngle` | Double | Blade pitch in degrees |
| `GeneratorLoad` | Double | Percent of rated power |
| `Status` | String | `RUNNING` or `OFFLINE` (derived, never set by hand) |
| `Command` | String | `RUN` (default) or `STOP`; `STOP` forces the turbine offline |

`Command` is held in memory only, so a container restart resets it to `RUN`.

### 8.2 Avation lamp controller

File: [Controllers/Avation/Avation.py](Controllers/Avation/Avation.py)

One container per turbine, simulating its aviation warning light. Endpoint: `opc.tcp://0.0.0.0:4840/blink/`, namespace `urn:blink`, root object `AviationLamp`. Every instance uses port 4840 on its own IP.

| Variable | Type | Writable | Meaning |
|---|---|---|---|
| `EnableBlink` | Boolean | yes | Lamp on/off (default on) |
| `SimulateStuck` | Boolean | yes | Stops toggling to simulate a failed lamp |
| `Blink` | Boolean | no | Current lamp state |
| `Healthy` | Boolean | no | `false` when the lamp is enabled but has not changed state within the timeout |
| `PeriodMs` | UInt32 | yes | Toggle period (minimum 100 ms, default 1000) |
| `TimeoutMs` | UInt32 | yes | Health timeout (at least twice the period, default 3000) |

The control loop runs every 100 ms. When `EnableBlink` is false the lamp is forced off.

### 8.3 Ground Controller

File: [Controllers/Ground_Controller/Ground_Controller.py](Controllers/Ground_Controller/Ground_Controller.py)

The aggregation hub. It acts as an OPC UA **client** towards the field devices and as an OPC UA **server** towards the supervision side.

- **Server:** `opc.tcp://0.0.0.0:4880/ground/`, namespace `urn:aenaon:ground`. It builds one `GroundController` object with one folder per turbine from `turbines.json` (see [section 13](#13-opc-ua-address-space-reference)).
- **Poll loop:** every `POLL_INTERVAL` seconds (default 2) it connects to each turbine and each lamp controller, copies their values into its own nodes and sets per-device `Connected` flags. If a device cannot be reached its values fall back to defaults and `Connected` becomes false.
- **Hostnames:** assets are found by `<OPC_HOST_PREFIX>_<n>` (`turbine_1`, …) and `<AVN_HOST_PREFIX>_<n>` (`avation_1`, …), with turbine ports `4860 + n − 1` and lamp port 4840.
- **Console:** it prints a status table of every turbine and lamp each cycle.

**Command relays** (the only write path from the supervision side into the field network):

| Ground Controller node | Behaviour | Target on the field device |
|---|---|---|
| `Turbine/CommandTurbine` (String) | One-shot: if the value is `RUN` or `STOP` it is written to the turbine, then cleared to an empty string | `Command` |
| `Aviation/CommandEnableBlink` (Boolean) | Level: the desired state is written to the lamp whenever it differs | `EnableBlink` |

The turbine's `Command` value is read back and republished on the Ground Controller's `Turbine/Command` node, so supervisors see the turbine's real state, including a `STOP` written directly on the turbine.

### 8.4 HMI / SCADA

Files: [SubStation/HMI/HMI-SCADA.py](SubStation/HMI/HMI-SCADA.py), [SubStation/Services/DB.py](SubStation/Services/DB.py), templates and static assets under `SubStation/`.

A FastAPI application served by `uvicorn` on port 5000 (published as 5001).

**Background tasks** (started at application start-up):

| Task | Function |
|---|---|
| `opc_poll_loop` | Connects to the Ground Controller as the `hmi` service account, reads every turbine and lamp each `POLL_INTERVAL`, and keeps the latest values in an in-memory store. On a failed session it reconnects after 5 s. Each cycle checks that the session is alive, and if every turbine read fails it forces a reconnect (this is what lets it recover after the Ground Controller restarts). |
| `db_cleanup_loop` | Hourly, deletes stored metrics older than `METRICS_RETENTION_DAYS` (default 7). |

**Pages**

| Page | Purpose |
|---|---|
| `login.html` | Sign-in form with username, password and an access key |
| `dashboard.html` | Live view of the farm: per-turbine metrics, totals, lamp state, data-flow pipeline |
| `control.html` | Turbine Start/Stop and lamp on/off. Actions are logged and pushed live to all open pages |
| `network_topology.html` | Schematic topology view. It is a conceptual diagram of a wind-farm network, including illustrative assets (substation, monitoring systems, cloud and OEM links) that are not separate containers in this stack |

**Display status.** The HMI shows `STOPPED` when the turbine's `Command` is `STOP`, `RUNNING` when the turbine reports running and is reachable, and `OFFLINE` otherwise.

**Live updates.** Pages poll `/get-current-metrics` and also subscribe to the `/events` Server-Sent Events stream, which broadcasts command events (for example "TURB002 stopped by admin") to every connected browser.

---

## 9. Data flow and timing

**Telemetry (read path)**

```
turbine (tick 2 s) ─► Ground Controller (poll 2 s) ─► HMI (poll 2 s) ─► browser (poll + SSE)
```

**Turbine command (write path)**

```
Operator clicks Stop ─► HMI  POST /control/<id>/stop
   ─► writes CommandTurbine="STOP" on the Ground Controller   (login: hmi)
   ─► Ground Controller poll forwards it to turbine Command    (login: gc)
   ─► turbine's next tick goes OFFLINE
   ─► Ground Controller reads Command back ─► HMI shows STOPPED
```

The path has three 2-second stages, so a command appears on the dashboard after roughly 2 to 6 seconds. Lamp commands follow the same pattern through `CommandEnableBlink`.

Because the display is derived from the turbine's own `Command` variable, a `STOP` written directly over OPC UA to a turbine appears on the dashboard exactly like one issued from the HMI. The HMI logs only commands issued through its own buttons.

---

## 10. Security model

### 10.1 OPC UA accounts

Each OPC UA server rejects anonymous connections and authenticates with username and password.

| Server | Account | Role | Permissions |
|---|---|---|---|
| Ground Controller (`:4880`) | `operator` | User | Read only |
| Ground Controller (`:4880`) | `hmi` | Admin | Read and write (service account used by the HMI) |
| Turbines and lamp controllers | `gc` | Admin | Read and write (service account used by the Ground Controller) |

Role enforcement uses `asyncua`'s `SimpleRoleRuleset` (Admin read+write, User read-only, Anonymous no access). Password comparison uses `hmac.compare_digest`. Authentication is enabled on a server when its password variable is set; if it is unset, that server falls back to anonymous access.

### 10.2 Who can change what

| Credential | Reachable from | Can write |
|---|---|---|
| `operator` | Ground Controller | Nothing |
| `hmi` | Ground Controller | `CommandTurbine` (start/stop any turbine) and `CommandEnableBlink` (any lamp), relayed by the Ground Controller |
| `gc` | Field network only | `Command` and lamp variables directly on the devices |

The Substation (HMI) is an OPC UA client and a web application. It has no OPC UA server, so nothing can be written to it over OPC UA. OPC UA clients can only influence what it displays.

### 10.3 Network isolation

- Turbines and lamp controllers sit on `field_net` and are not reachable from `challenge_net`.
- Reaching them requires code running on the Ground Controller, the only container on both networks.

### 10.4 HMI web authentication

The HMI uses session-cookie login (username, password and an access key) with two roles, `admin` and `user`. The default administrator account is seeded on first run in [SubStation/Services/DB.py](SubStation/Services/DB.py) and should be changed on any real deployment.

### 10.5 Current limits

- The OPC UA channel uses the `NoSecurity` policy, so usernames and passwords travel unencrypted. Certificates with `SignAndEncrypt` are the planned next layer and are not implemented.
- The default passwords live in `docker-compose.yml`. Use an `.env` file to override them.
- Container logging is disabled (`logging: driver: none`), so `docker logs` returns nothing.
- Some HMI weaknesses are deliberate parts of the training scenario and are not described here.

---

## 11. Configuration

### 11.1 Environment variables

| Variable | Service | Default | Purpose |
|---|---|---|---|
| `TURBINE_ID` | turbine, avation | `TURB001` | Turbine this container represents (sets the turbine port) |
| `TURBINE_HOST` | avation | — | Hostname of the paired turbine |
| `OPC_GC_USER` | turbine, avation, ground_controller | `gc` | Field-network account name |
| `OPC_GC_PASSWORD` | turbine, avation, ground_controller | empty | Field-network password. Empty disables login on the field devices |
| `TURBINES_JSON` | ground_controller, hmi | `turbines.json` | Path of the turbine catalogue |
| `OPC_HOST_PREFIX`, `AVN_HOST_PREFIX` | ground_controller | `turbine`, `avation` | Hostname prefixes for field devices |
| `POLL_INTERVAL` | ground_controller, hmi | `2` | Poll interval in seconds |
| `GC_PORT` | ground_controller, hmi | `4880` | Ground Controller port |
| `OPC_OPERATOR_USER`, `OPC_OPERATOR_PASSWORD` | ground_controller | `operator`, empty | Read-only account. An empty password disables login |
| `OPC_ADMIN_USER`, `OPC_ADMIN_PASSWORD` | ground_controller | `hmi`, empty | Write-capable service account |
| `GC_HOST`, `GC_NAMESPACE` | hmi | `ground_controller`, `urn:aenaon:ground` | Where the HMI finds the Ground Controller |
| `OPC_USER`, `OPC_PASSWORD` | hmi | empty | Credentials the HMI uses towards the Ground Controller |
| `HMI_PORT` | hmi | `5000` | HMI listen port |
| `METRICS_RETENTION_DAYS` | hmi | `7` | Retention of stored metrics |

### 11.2 Overriding secrets from the host

`docker-compose.yml` reads these host variables, falling back to built-in defaults:

| Host variable | Feeds |
|---|---|
| `OPC_OPERATOR_PASSWORD` | Ground Controller `operator` password |
| `OPC_HMI_PASSWORD` | Ground Controller `hmi` password and the HMI's `OPC_PASSWORD` (they must match) |
| `OPC_GC_PASSWORD` | Field-network `gc` password on every device |

Put them in a git-ignored `.env` file next to `docker-compose.yml`, then recreate the stack.

### 11.3 Turbine catalogue

[common/turbines.json](common/turbines.json) defines the farm name and, for each turbine, `id`, `model`, `capacity_kw` and `location` (latitude, longitude). The Ground Controller builds its address space from it and the HMI uses it for the dashboard.

---

## 12. Running the project

Requirements: Docker with Docker Compose.

```bash
docker compose up -d --build     # build and start all 8 containers
docker compose ps                # check status
docker compose down              # stop and remove containers
docker compose down -v           # also delete the HMI database volume
```

| Endpoint | URL |
|---|---|
| HMI | http://localhost:5001 |
| Ground Controller (OPC UA) | `opc.tcp://localhost:4880/ground/` |

Rebuild a single service after changing its code:

```bash
docker compose up -d --build hmi
```

Restarting only the Ground Controller is safe: the HMI reconnects by itself within a few seconds.

**Talking to the OPC UA server from the host**

```bash
python3.13 -m venv .venv && source .venv/bin/activate
pip install asyncua==1.1.5
```

Use Python 3.13 or earlier on the host. Connect with the `operator` account for read access. The local `opc_client.py` helper (not committed) wraps this as a command-line tool with `status`, `watch`, `browse`, `read` and `lamp` commands.

---

## 13. OPC UA address space reference

### 13.1 Ground Controller — `urn:aenaon:ground`

```
Objects
└── GroundController
    └── <TURBxxx>                      one per entry in turbines.json
        ├── Meta
        │   ├── Model          String
        │   ├── CapacityKW     Double
        │   ├── Latitude       Double
        │   └── Longitude      Double
        ├── Turbine
        │   ├── Connected      Boolean   field device reachable
        │   ├── RPM, Temperature_C, PowerOutput_kW,
        │   │   WindSpeed, PitchAngle, GeneratorLoad    Double  (mirrored)
        │   ├── Status         String    (mirrored)
        │   ├── Command        String    (mirrored from the turbine)
        │   └── CommandTurbine String    writable, one-shot RUN / STOP
        └── Aviation
            ├── Connected      Boolean
            ├── Blink, Healthy, EnableBlink   Boolean  (mirrored)
            ├── PeriodMs       UInt32    (mirrored)
            └── CommandEnableBlink  Boolean  writable
```

### 13.2 Turbine — `urn:aenaon:turbine`

`Objects/Turbine_<ID>/{RPM, Temperature_C, PowerOutput_kW, WindSpeed, PitchAngle, GeneratorLoad, Status, Command}` at `opc.tcp://turbine_<n>:<4860+n-1>/turbine/`.

### 13.3 Avation lamp — `urn:blink`

`Objects/AviationLamp/{EnableBlink, SimulateStuck, Blink, Healthy, PeriodMs, TimeoutMs}` at `opc.tcp://avation_<n>:4840/blink/`.

---

## 14. HTTP reference

HMI base URL: `http://localhost:5001`.

| Method | Path | Description |
|---|---|---|
| GET | `/` | Redirects to `/login` |
| GET, POST | `/login` | Sign-in form (username, password, access key) and submit |
| GET | `/logout` | Clears the session |
| GET | `/dashboard` | Live dashboard |
| GET | `/control` | Control console |
| GET | `/Network-Topology` | Topology diagram |
| GET | `/get-current-metrics` | JSON: all turbines with live metrics, lamp state, online/offline counts and total power |
| GET | `/metrics` | JSON: latest stored metrics per turbine from the database |
| GET | `/get-logs` | JSON: command history, newest first (admin only). Query: `turbine_id`, `limit` (default 50) |
| GET | `/events` | Server-Sent Events stream of command events |
| POST | `/control/{turbine_id}/stop` | Writes `STOP` through the Ground Controller |
| POST | `/control/{turbine_id}/start` | Writes `RUN` through the Ground Controller |
| POST | `/control/{turbine_id}/lamp?enable=true\|false` | Sets the lamp command through the Ground Controller |

Control endpoints return `{"ok": true, …}` on success and HTTP 502 with `{"ok": false, "error": …}` if the Ground Controller cannot be reached. Access control varies by endpoint and is part of the training scenario.

`/get-current-metrics` response shape:

```json
{
  "turbines": [
    {
      "id": "TURB001", "model": "AENAON-X100", "capacity_kw": 3000,
      "location": "34.0522, -118.2437",
      "online": true, "last_update": "Live", "status": "RUNNING",
      "rpm": 12.3, "temperature": 35.1, "power_output_kw": 1890.4,
      "wind_speed": 9.2, "pitch_angle": 3.1, "generator_load": 63.0,
      "blink": true, "lamp_healthy": true, "lamp_enabled": true, "lamp_period_ms": 1000
    }
  ],
  "total_turbines": 4, "online_count": 3, "offline_count": 1, "total_power_kw": 5400.0
}
```

---

## 15. Database

SQLite file `/app/data/hmi.db`, persisted in the `hmi_data` Docker volume. Tables are created automatically at start-up.

| Table | Columns | Written by |
|---|---|---|
| `users` | `id`, `username` (unique), `password_hash`, `special_key`, `role` (`admin` or `user`) | Seeded on first run |
| `turbine_metrics` | `id`, `turbine_id`, `rotor_speed`, `pitch_angle`, `status`, `timestamp` | HMI poller, one row per turbine per poll |
| `turbine_commands` | `id`, `turbine_id`, `command`, `issued_by`, `timestamp` | HMI control endpoints (`STOP`, `START`, `LAMP_ON`, `LAMP_OFF`) |
| `metric_changes` | `id`, `turbine_id`, `metric_name`, `old_value`, `new_value`, `changed_by`, `timestamp` | Defined, but nothing writes to it in the current code |

`turbine_metrics` grows by one row per turbine every poll interval; the hourly cleanup keeps only the last `METRICS_RETENTION_DAYS` days.

---

## 16. Extending the project

### 16.1 Add a turbine (for example TURB004)

`common/turbines.json` already lists `TURB004`, but no containers exist for it, so it shows as offline. To deploy it:

1. In `docker-compose.yml`, add `turbine_4` (`TURBINE_ID=TURB004`, `OPC_GC_PASSWORD`, `field_net` IP `10.10.2.10`) and `avation_4` (`TURBINE_ID=TURB004`, `TURBINE_HOST=turbine_4`, `OPC_GC_PASSWORD`, IP `10.10.2.11`), copying the existing pairs.
2. Add both to the Ground Controller's `depends_on`.
3. Run `docker compose up -d --build`.

The turbine's port is derived automatically (`4860 + 4 − 1 = 4863`). To add a brand-new turbine ID instead, also add it to `turbines.json`.

### 16.2 Add a variable to a turbine

1. Add it to `VAR_DEFS` in `Turbine_Simulator.py` and compute it in `_Physics.step()`.
2. Add it to `TURBINE_VARS` in `Ground_Controller.py` so it is mirrored.
3. Add it to `turbine_vars` in `HMI-SCADA.py` and expose it in `get_current_metrics`.

### 16.3 Add a new command

Follow the existing relay pattern: a writable command node on the Ground Controller, a forwarding step in the matching `poll_*` function, and a control endpoint in the HMI that writes the node. Use a one-shot node (clear after forwarding) for actions and a level node (compare and write) for desired states.

### 16.4 Credentials

Change passwords through the `.env` variables listed in [section 11.2](#112-overriding-secrets-from-the-host), then recreate the stack. Remember that `OPC_HMI_PASSWORD` feeds both the Ground Controller and the HMI.

---

## 17. Troubleshooting

| Symptom | Likely cause and fix |
|---|---|
| `docker logs <container>` prints nothing | Logging is disabled in compose. Remove the `logging:` blocks to debug |
| HMI shows every turbine offline | Check that `ground_controller` is up (`docker compose ps`). The HMI reconnects within about 5 s once it is back |
| One turbine always shows offline or fault | `TURB004` is in `turbines.json` but has no containers ([16.1](#161-add-a-turbine-for-example-turb004)) |
| Dashboard lags after a command | Expected: up to three 2-second stages ([section 9](#9-data-flow-and-timing)) |
| Turbine returns to `RUNNING` after a restart | `Command` is not persisted and resets to `RUN` |
| `BadUserAccessDenied` from an OPC UA client | Wrong user or password, or the account's role lacks write permission |
| `Sending plain-text password` warning | Expected: the channel is not yet encrypted ([10.5](#105-current-limits)) |
| `TypeError: issubclass() arg 1 must be a class` on the host | Python 3.14 with `asyncua` 1.1.5. Use Python 3.13 |
| `ModuleNotFoundError: asyncua` on the host | Activate the virtual environment or `pip install asyncua==1.1.5` |
| Compose refuses to start with a template error | You are using a Cyber Range template. Use the standalone `docker-compose.yml` |
