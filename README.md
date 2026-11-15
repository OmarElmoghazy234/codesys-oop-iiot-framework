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

## 🚀 Roadmap
* [x] **Phase 1:** Decoupled Runtime Setup & Persistent Memory Architecture
* [ ] **Phase 2:** Advanced IEC 61131-3 OOP (Interfaces, Polymorphism, `SUPER^` Inheritance)
* [ ] **Phase 3:** Virtual Fieldbus Topologies (EtherCAT & PROFINET)
* [ ] **Phase 4:** WebVisu HMI & IT/OT Integration (OPC UA / MQTT JSON)
* [ ] **Phase 5:** Containerized Linux Edge Deployment (Docker Control SL)
