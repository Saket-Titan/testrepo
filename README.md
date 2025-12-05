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



# Failure Mode and Effects Analysis (FMEA)

**System:** Firmware Safety Mechanisms
**Reviewer:** Saket Kumar

| ID | Component | Failure Mode | Effect (Vehicle Level) | Safety Mechanism (Detection & Control) | Diagnostic Coverage | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **F-01** | **Engine MCU** | Software Hang / Infinite Loop | **SG-03 Violation:** Loss of control; frozen RPM output. | **Watchdog Timer (WDT):** Hardware watchdog resets MCU if not fed within 100ms. | High (99%) | ✅ Implemented |
| **F-02** | **Sensor Input** | Electrical Short (Stuck High) | **SG-01 Violation:** Unintended Acceleration (Reads 25,000 RPM). | **Plausibility Check:** Software logic rejects values > MAX_RPM (8000). Flags "Safety Violation". | High (99%) | ✅ Implemented |
| **F-03** | **CAN Bus** | Wire Break / Disconnect | **SG-02 Violation:** Body ECU receives no brake commands. | **Timeout Monitor:** Body ECU detects bus silence > 100ms and enters Safe State (Default OFF). | Medium (90%) | ✅ Implemented |
| **F-04** | **Firmware Image** | Malware Injection / Tampering | **Security Violation:** Malicious code execution overriding safety logic. | **Secure Boot:** MCUboot verifies RSA-2048 signature before booting. Rejects unsigned images. | High (99%) | ✅ Implemented |
| **F-05** | **CAN Bus** | Injection Attack (Spoofing) | **SG-01 Violation:** Hacker sends fake "High RPM" frames. | **AI Intrusion Detection:** Autoencoder detects data anomaly (Score > 0.1) and triggers alert. | High (95%) | ✅ Implemented |


# Automotive Safety & Security ECU Prototype
**ISO 26262 Functional Safety | TinyML Intrusion Detection | Zephyr RTOS**

[![Build Firmware](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME/actions/workflows/ci.yml/badge.svg)](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🚗 Executive Summary
This project demonstrates a production-grade automotive architecture integrating **Functional Safety (ASIL-B)** and **Cybersecurity (Edge AI)**.

The system consists of a distributed architecture with two ECUs communicating over **CAN-FD** using the **SAE J1939** protocol. It features a "Safety Node" (Engine ECU) that enforces real-time plausibility checks and a "Security Node" (Body ECU) that utilizes an on-device **Autoencoder Neural Network** to detect CAN bus anomalies in under 2ms.

## 🏗️ System Architecture

```mermaid
graph LR
    subgraph "Engine ECU (Safety Node)"
    A[STM32G474RE] -- "J1939 (PGN 61444)" --> CAN
    A -- "Watchdog & Plausibility" --> A
    end

    subgraph "CAN-FD Bus"
    CAN[MCP2562 Transceivers]
    end

    subgraph "Body ECU (Security Node)"
    CAN --> B[STM32H723ZG]
    B -- "TinyML Inference" --> AI[Anomaly Detector]
    AI -- "Score > 0.1" --> Alert[Safe State Alert]
    end


Key FeaturesDomainFeatureImplementation DetailsFunctional SafetyFault DetectionImplemented Watchdog Timer (WDT) and Data Plausibility Checks (Range/Gradient) to meet ASIL-B goals.NetworkingJ1939 & UDSCustom J1939 PGN construction and UDS Service 0x10 (Diagnostic Session Control) implementation.AI / SecurityTinyML IDSCustom C-based Inference Engine running an Autoencoder (76 parameters) to detect malicious CAN injection.LifecycleSecure BootImplemented MCUboot chain-of-trust with RSA-2048 signed firmware and A/B partition OTA swapping.ArchitectureEdge ComputingDesigned edge-first architecture to process anomalies locally, reducing cloud bandwidth dependencies.
