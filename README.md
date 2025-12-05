# testrepo

## editing this file

This is just a markup for this file.
# Hazard Analysis and Risk Assessment (HARA)
**Project:** Edge-AI Intrusion Detector & Safety ECU
**Item Definition:** Secure Automotive Gateway System (Engine & Body Control)
**Standard:** ISO 26262:2018

## 1. Item Definition
The item is a distributed automotive control system consisting of an Engine Control Unit (ECU), a Body Control Unit (BCM), and a Gateway communicating over CAN-FD. The system manages torque requests (simulated) and critical body actuators (brake lights) while monitoring for cyber-physical threats.

## 2. Hazard Analysis
We analyzed potential malfunctioning behaviors at the vehicle level.

| ID | Malfunctioning Behavior | Operational Situation | Severity (S) | Exposure (E) | Controllability (C) | ASIL |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **H-01** | **Unintended Acceleration** (Engine ECU sends max torque without input) | Highway Driving (>100 km/h) | **S3** (Life-threatening) | **E4** (High Probability) | **C3** (Difficult to control) | **ASIL D** |
| **H-02** | **Loss of Deceleration/Brake Light** (Body ECU fails to actuate light) | Heavy City Traffic | **S2** (Severe Injuries) | **E4** (High Probability) | **C2** (Normally Controllable) | **ASIL B** |
| **H-03** | **System Freeze / Communication Loss** (Gateway/Engine stops responding) | Overtaking Maneuver | **S3** (Life-threatening) | **E3** (Medium Probability) | **C3** (Difficult to control) | **ASIL D** |

* **S3:** Life-threatening injuries (survival uncertain).
* **E4:** Situation occurs >10% of drive time.
* **C3:** Average driver cannot control the outcome (requires expert skill).

## 3. Safety Goals (SG)
Derived from the ASIL ratings, the system must meet these goals:

* **SG-01 (ASIL D):** The system shall prevent unintended torque requests exceeding 10% of the driver demand.
* **SG-02 (ASIL B):** The system shall indicate braking intent to following vehicles with >99% reliability.
* **SG-03 (ASIL D):** The system shall detect a loss of communication or frozen ECU within **100ms** and transition to a Safe State.
