# CODESYS V3 Industrial Automation Sandbox

An accelerated, hardware-agnostic industrial automation architecture built in **CODESYS V3.5 SP22**. This repository documents a phased engineering progression transitioning from Beckhoff TwinCAT 3 paradigms to decoupled CODESYS runtimes, Object-Oriented IEC 61131-3 programming, multi-protocol fieldbuses, and IT/OT edge integrations.

## 🏗️ Architecture Overview

Unlike kernel-coupled real-time environments, this sandbox utilizes a decoupled client-server architecture:
* **IDE Shell:** CODESYS Development System V3.5
* **Routing Layer:** CODESYS Edge Gateway (`localhost` / TCP Port `1217`)
* **Target Runtime:** CODESYS Control Win V3 x64 (User-mode background service)

## ✅ Phase 1: Runtime Foundation & Persistent Uptime Logger (Completed)

* **Gateway & Port Mapping:** Configured local loopback routing via TCP Port `1217` to isolate the virtual PLC runtime from the host OS.
* **Heartbeat & Cycle Execution:** Implemented deterministic 1-second and 60-second pulse timers (`TON`) inside `PLC_PRG`.
* **Non-Volatile Memory Recovery:** Utilized `VAR RETAIN PERSISTENT` memory allocation to log cumulative runtime seconds and minutes, surviving simulated runtime shutdowns and service restarts without data loss.

## ✅ Phase 2: Object-Oriented Actuator Framework (Completed)

* **Interface Contract (`I_Actuator`):** Defined a standardized method contract (`Start`, `Stop`, `CyclicUpdate`) to decouple machine control logic from underlying hardware implementations.
* **Base Class & Inheritance (`FB_ActuatorBase`):** Encapsulated shared operational states (`_bRunning`, `_bFault`) and runtime tracking (`TON`) to eliminate duplicate logic across derived function blocks.
* **Method Overriding & `SUPER^` (`FB_Motor` & `FB_Valve`):** Extended the base class to implement device-specific behaviors—including simulated speed ramping for motors and open/close position feedback for valves—while preserving parent execution via `SUPER^.CyclicUpdate()`.
* **Polymorphic Execution:** Bound heterogeneous actuator instances into a unified `ARRAY[1..3] OF I_Actuator` inside `PLC_PRG` to execute synchronized master commands across diverse device types in a single scan loop.

## 🚀 Roadmap
* [x] **Phase 1:** Decoupled Runtime Setup & Persistent Memory Architecture
* [x] **Phase 2:** Advanced IEC 61131-3 OOP (Interfaces, Polymorphism, `SUPER^` Inheritance)
* [ ] **Phase 3:** Virtual Fieldbus Topologies (EtherCAT & PROFINET)
* [ ] **Phase 4:** WebVisu HMI & IT/OT Integration (OPC UA / MQTT JSON)
* [ ] **Phase 5:** Containerized Linux Edge Deployment (Docker Control SL)
