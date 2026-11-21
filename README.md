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
* **Non-Volatile Memory Recovery:** Utilized `VAR RETAIN PERSISTENT` memory allocation paired with a `PersistentVars` instance list to log cumulative runtime seconds and minutes, surviving simulated runtime shutdowns and service restarts without data loss.

## ✅ Phase 2: Object-Oriented Actuator Framework (Completed)

* **Interface Contract (`I_Actuator`):** Defined a standardized method contract (`Start`, `Stop`, `ResetFault`, `CyclicUpdate`) to decouple machine control logic from underlying hardware implementations.
* **Base Class & Inheritance (`FB_ActuatorBase`):** Encapsulated shared operational states (`_bRunning`, `_bFault`), fault recovery, and runtime tracking (`TON`) to eliminate duplicate logic across derived function blocks.
* **Method Overriding & `SUPER^` (`FB_Motor`, `FB_Valve`, & `FB_Heater`):** Extended the base class to implement device-specific behaviors—including speed ramping for motors, open/close position feedback for valves, and thermal heating/cooling dynamics for heaters—while preserving parent execution via `SUPER^.CyclicUpdate()`.
* **Polymorphic Execution:** Bound four heterogeneous actuator instances (`fbConveyorMotor`, `fbCoolingPump`, `fbMainValve`, `fbTankHeater`) into a unified `ARRAY[1..4] OF I_Actuator` inside `PLC_PRG` to execute synchronized master start, stop, and fault-reset commands in a single scan loop.

## ✅ Phase 3: Virtual Fieldbus Topologies — EtherCAT & PROFINET (Completed)

* **Multi-Protocol Master Configuration:** Configured both an `EtherCAT_Master` and a `PN_Controller` (PROFINET IO Master) bound to the host network interface (`192.168.0.194`) with dedicated cyclic bus tasks.
* **Virtual Slave Hardware & Process Mapping:** Integrated a Beckhoff `EP7041-3002` EtherCAT stepper output stage and a `CODESYS_PROFINET_Device` (`Output_8Bit`), mapping cyclic process data (`%QX4.0`, `%QW5`, `%QX8.0`) to a decoupled I/O exchange layer in `PLC_PRG`.
* **Encapsulated Property Accessors:** Exposed read-only `IsRunning` and `ActualSpeed` properties on `FB_ActuatorBase` and `FB_Motor` to feed cyclic fieldbus frames without breaking OOP encapsulation.
* **Fieldbus Fault Interlock:** Implemented a bus-health safety interlock (`bFieldbusFault`) that immediately commands a polymorphic emergency stop across all actuators upon simulated cable dropout.


## 🚀 Roadmap
* [x] **Phase 1:** Decoupled Runtime Setup & Persistent Memory Architecture
* [x] **Phase 2:** Advanced IEC 61131-3 OOP (Interfaces, Polymorphism, `SUPER^` Inheritance)
* [x] **Phase 3:** Virtual Fieldbus Topologies (EtherCAT & PROFINET)
* [ ] **Phase 4:** WebVisu HMI & IT/OT Integration (OPC UA / MQTT JSON)
* [ ] **Phase 5:** Containerized Linux Edge Deployment (Docker Control SL)
