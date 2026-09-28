# 🚗 In-Vehicle Infotainment (IVI) System

## Conceptual Architecture Integrating Media, Navigation & Smartphone Projection

A conceptual **In-Vehicle Infotainment (IVI)** system architecture designed around **Android Automotive concepts**, integrating:

* 🎵 Media Playback
* 🗺️ Navigation
* 📱 Smartphone Projection
* 🎙️ Voice & Hands-Free Interaction
* 🚘 Vehicle Data Integration
* 🔊 Automotive Audio Management

The project demonstrates how user input, application services, vehicle data, sensor information, and hardware communicate through a **layered automotive software architecture**.

> **Project Type:** Conceptual Architecture & System Design Study
> **Reference Architecture:** Android Automotive concepts
> **Implementation:** Design/modeling only
> **Hardware:** No real vehicle hardware required

---

## 📌 Table of Contents

* [Project Overview](#-project-overview)
* [Objectives](#-objectives)
* [System Architecture](#️-system-architecture)
* [Architecture Layers](#architecture-layers)
* [Component Interfaces](#-component-interfaces)
* [End-to-End Data Flows](#-end-to-end-data-flows)
* [Combined Navigation & Music Scenario](#-combined-navigation--music-scenario)
* [Vehicle Data Path](#-vehicle-data-path)
* [Design Principles](#-design-principles)
* [Tools & Environment](#️-tools--environment)
* [Testing & Verification](#-testing--verification)
* [Project Results](#-project-results)
* [Safety & Quality Considerations](#-safety--quality-considerations)
* [Scope & Limitations](#️-scope--limitations)
* [Future Scope](#-future-scope)
* [References](#-references)

---

# 📖 Project Overview

An **In-Vehicle Infotainment (IVI)** system is the combined hardware and software platform located in a vehicle's dashboard.

Typical IVI functionality includes:

* Media playback
* Navigation
* Smartphone integration
* Voice control
* Hands-free interaction
* Vehicle information
* Selected vehicle controls

This project focuses on a conceptual architecture connecting three primary functions:

1. **Media Playback**
2. **Navigation**
3. **Smartphone Projection**

The architecture separates applications from automotive services, hardware abstraction layers, and physical hardware.

This provides a structured model for understanding how modern automotive infotainment systems communicate internally.

---

# 🎯 Objectives

The primary objectives of this project are to:

* Identify the major components required for media, navigation, and smartphone projection.
* Design a clear layered IVI architecture.
* Define and document component interfaces.
* Identify the protocol, API, or bus used by each interface.
* Describe the data exchanged between components.
* Trace complete end-to-end data flows.
* Verify the architecture against project requirements.
* Demonstrate interaction between navigation audio and music through **audio focus**.

---

# 🏗️ System Architecture

The proposed IVI system uses a **six-layer conceptual architecture**.

```text
┌─────────────────────────────────────────┐
│                  DRIVER                 │
│       Touchscreen | Microphone | Keys   │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│                   APPS                  │
│      Media App | Navigation | Projection│
└────────────────────┬────────────────────┘
                     │
                  Binder / AIDL
                     │
┌────────────────────▼────────────────────┐
│                SERVICES                 │
│    Media | Location | Projection       │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│               CAR LAYER                 │
│   Car Audio Service | Car Property     │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│                   HAL                   │
│ Audio HAL | GNSS HAL | Vehicle HAL     │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│                HARDWARE                │
│ Speakers | GPS | CAN/ECUs | Smartphone │
└─────────────────────────────────────────┘
```

---

## Architecture Layers

| Layer         | Main Components                              | Responsibility                                       |
| ------------- | -------------------------------------------- | ---------------------------------------------------- |
| **Driver**    | Touchscreen, microphone, steering-wheel keys | Provides user input and receives audio/visual output |
| **Apps**      | Media, Navigation, Projection                | Provides user-facing functionality                   |
| **Services**  | Media, Location, Projection Services         | Manages application-level operations                 |
| **Car Layer** | Car Audio Service, Car Property Service      | Handles automotive-specific audio and vehicle data   |
| **HAL**       | Audio HAL, GNSS HAL, Vehicle HAL             | Provides standardized hardware interfaces            |
| **Hardware**  | Speakers, GPS receiver, CAN/ECUs, smartphone | Physical devices and external systems                |

---

# 🔌 Component Interfaces

The architecture defines **13 component-to-component interfaces**.

| ID      | Connection                                | Interface / Protocol                            | Main Data                                            |
| ------- | ----------------------------------------- | ----------------------------------------------- | ---------------------------------------------------- |
| **I1**  | Driver ↔ Apps                             | Linux Input Subsystem / Android Input Framework | Touch, keys, voice commands, screen and sound output |
| **I2**  | Media App ↔ Media Service                 | Binder IPC, MediaSession / MediaBrowser APIs    | Play, pause, skip, metadata, playback state          |
| **I3**  | Navigation App ↔ Location Service         | Binder IPC, LocationManager API                 | Location requests and updates                        |
| **I4**  | Projection App ↔ Projection Service       | Binder IPC, car projection APIs                 | Video, audio, touch events, session state            |
| **I5**  | Media Service ↔ Car Audio Service         | AudioManager / Car API                          | Audio focus and volume                               |
| **I6**  | Projection Service ↔ Car Property Service | Binder IPC, Car API                             | Driving state, speed, gear                           |
| **I7**  | Car Audio Service ↔ Audio HAL             | HAL Interface                                   | Routing, volume, PCM audio                           |
| **I8**  | Location Service ↔ GNSS HAL               | HAL Interface                                   | Location fixes and satellite status                  |
| **I9**  | Car Property Service ↔ Vehicle HAL        | Vehicle HAL Interface                           | Vehicle properties such as speed and gear            |
| **I10** | Audio HAL ↔ Speakers/Amplifier            | ALSA / TinyALSA, I2S/TDM                        | Digital audio samples and controls                   |
| **I11** | GNSS HAL ↔ GPS Receiver                   | UART / SPI / SoC                                | Position data                                        |
| **I12** | Vehicle HAL ↔ CAN/ECUs                    | CAN through gateway/microcontroller             | Vehicle signals and status                           |
| **I13** | Projection Service ↔ Smartphone           | USB / Wi-Fi with Bluetooth setup                | Video, audio, touch and vehicle data                 |

---

# 🔄 End-to-End Data Flows

The project models three complete end-to-end flows.

## 1. 🎵 Media Playback

```text
Driver
   ↓
Media App
   ↓
Media Service
   ↓
Car Audio Service
   ↓
Audio HAL
   ↓
Speakers / Amplifier
```

### Process

**A1.** Driver presses Play.

**A2.** Media App sends the play command to Media Service.

**A3.** Media Service requests audio focus from Car Audio Service.

**A4.** Car Audio Service determines routing and volume.

**A5.** Audio HAL sends digital audio samples to the speakers/amplifier.

---

## 2. 🗺️ Navigation

```text
GPS Receiver
     ↓
GNSS HAL
     ↓
Location Service
     ↓
Navigation App
     ↓
Driver
```

### Process

**B1.** GPS receiver provides position data to GNSS HAL.

**B2.** GNSS HAL provides the location fix to Location Service.

**B3.** Location Service sends location updates to Navigation App.

**B4.** Navigation App displays the route and provides voice guidance.

Navigation voice guidance uses the automotive audio path and audio-focus mechanism.

---

## 3. 📱 Smartphone Projection

```text
Smartphone
     ↓
Projection Service
     ↓
Projection App
     ↓
Car Display / Driver
```

### Process

**C1.** Smartphone sends video, audio, and session information.

**C2.** Projection Service sends the stream to Projection App.

**C3.** Projection App displays the phone interface on the car display.

Touch input can travel in the reverse direction from the car display back to the smartphone.

---

# 🎵 Combined Navigation & Music Scenario

One of the key interaction scenarios demonstrates how navigation and media playback coexist.

```text
Music Playing
     ↓
Navigation receives location
     ↓
Navigation needs to speak
     ↓
Navigation requests audio focus
     ↓
Car Audio Service applies focus rules
     ↓
Music volume is reduced
     ↓
Navigation prompt plays
     ↓
Prompt ends
     ↓
Music volume is restored
```

The **Car Audio Service** acts as the central point for audio-focus decisions.

In a typical configuration, music is temporarily **ducked** while a navigation prompt is played.

> **Note:** Exact audio behavior can vary by vehicle manufacturer.

---

# 🚗 Vehicle Data Path

Vehicle information travels from the vehicle network toward applications through the following path:

```text
CAN Bus / ECUs
      ↓
Vehicle HAL
      ↓
Car Property Service
      ↓
Projection Service / Other Apps
```

### Example Vehicle Data

* Vehicle speed
* Gear selection
* Other supported vehicle properties

Vehicle information can be used to limit distracting features while the vehicle is moving.

---

# 🧩 Design Principles

The architecture follows several important design rules:

### 1. Application-to-Service Communication

Applications communicate with framework services through:

**Binder / AIDL**

### 2. Hardware Abstraction

Applications do not directly communicate with HALs or physical hardware.

### 3. Standardized Hardware Interfaces

Framework and car services access hardware through HAL interfaces.

### 4. Vendor Independence

Vendor-specific hardware implementation remains isolated within the HAL/driver layers.

### 5. Controlled Vehicle Access

Vehicle signals reach applications through the **Car Property Service**.

### 6. Centralized Audio Management

Audio focus is managed through the **Car Audio Service**.

These principles support hardware independence, safety controls, and maintainability.

---

# 🛠️ Tools & Environment

Since this is a **conceptual architecture and modeling study**, no specialized vehicle hardware or runtime environment is required.

## Tools Used

| Tool                                    | Purpose                                               |
| --------------------------------------- | ----------------------------------------------------- |
| **Microsoft Word / LibreOffice Writer** | Report preparation                                    |
| **Python + Matplotlib**                 | Architecture diagrams                                 |
| **Draw.io / diagrams.net**              | Diagram modeling and redrawing                        |
| **AOSP Documentation**                  | Android Automotive component and interface references |
| **Android for Cars Documentation**      | Media, navigation, and projection concepts            |

## Environment

A normal laptop or desktop with a web browser is sufficient.

The conceptual version does **not** require:

* Android Studio
* Android Emulator
* Raspberry Pi
* Real vehicle hardware

---

# 🧪 Testing & Verification

Because this is a conceptual project, testing is performed through **design verification rather than code execution**.

## Requirement Coverage

| Requirement            | Status |
| ---------------------- | ------ |
| Media integration      | ✅ Met  |
| Navigation integration | ✅ Met  |
| Projection integration | ✅ Met  |
| Interface annotations  | ✅ Met  |
| Data-flow annotations  | ✅ Met  |

## Consistency Checks

The architecture was checked to ensure that:

* Every diagram interface is represented in the interface table.
* No application directly connects to a HAL or hardware.
* Every numbered data-flow marker maps to a documented step.
* Each flow can be traced from source to destination.
* Component and HAL names remain consistent with AOSP terminology.

**All documented consistency checks passed.**

---

# 📊 Project Results

The final design contains:

| Metric                               | Result |
| ------------------------------------ | -----: |
| Architectural Layers                 |  **6** |
| Component Interfaces                 | **13** |
| End-to-End Data Flows                |  **3** |
| Numbered Data-Flow Steps             | **12** |
| Combined Navigation + Music Scenario |  **1** |
| Requirement Checks                   |  **5** |
| Consistency Checks                   |  **5** |

The design demonstrates how:

* Media
* Navigation
* Smartphone projection
* Audio management
* Location services
* Vehicle data

can operate together within an Android Automotive-style architecture.

---

# ⚠️ Scope & Limitations

This project is a **conceptual architecture study**.

It does **not** include:

* Real vehicle hardware
* Real CAN bus communication
* Production IVI source code
* Executed software
* Real-time performance measurements
* Android Automotive emulator testing
* Actual Android Auto implementation
* Actual Apple CarPlay implementation

Some real-world Android Automotive components are simplified or grouped to keep the architecture readable.

For example, the native audio server and audio policy are represented within the simplified Audio HAL connection.

---

# 🔐 Safety & Quality Considerations

## Driver Distraction

Navigation and projection interfaces should limit complex interactions while the vehicle is moving.

## Security

Applications and connected smartphones should not receive unrestricted access to vehicle networks.

## Latency

Touch interactions and audio should respond quickly.

Safety-critical functions such as warnings and rear-view camera functionality generally require stricter timing than normal infotainment features.

## Maintainability

The layered architecture allows different components to be updated or tested independently.

## Updates

Future OTA mechanisms can update applications and services without directly modifying safety-critical ECUs.

---

# 🚀 Future Scope

The conceptual architecture can be extended with:

* Android Automotive media application
* Android Automotive navigation application
* Android Automotive emulator testing
* Raspberry Pi-based experimentation
* Media Service and Audio HAL message-flow simulation
* UML-based IVI use cases
* Bluetooth telephony
* Rear-view camera functionality
* Instrument cluster integration
* Multi-zone audio
* OTA update architecture
* Cybersecurity components
* Real-world latency measurement
* System startup-time measurement

---

# 📚 References

The project is based primarily on Android Automotive and Android for Cars documentation:

1. **Android Open Source Project — Android Automotive OS**
2. **Android Open Source Project — Vehicle HAL**
3. **Android Open Source Project — Audio in Android Automotive**
4. **Android Developers — Android for Cars**
5. **Google — Android Auto**
6. **Apple — CarPlay**
7. **Module 6: IVI — In-Vehicle Infotainment Systems course material**

---

# 👨‍💻 Project Information

| Field                  | Details                                          |
| ---------------------- | ------------------------------------------------ |
| **Project**            | IVI — In-Vehicle Infotainment Systems            |
| **Topic**              | Conceptual Architecture of an IVI System         |
| **Focus**              | Media, Navigation & Smartphone Projection        |
| **Reference Platform** | Android Automotive Architecture                  |
| **Author**             | Rohit  Chatterjee                                |
| **Course**             | Wipro Automotive Course                          |
| **Department**         | Electronics and Communication Engineering        |
| **Institution**        | Institute of Engineering and Management, Newtown |

---

# 📌 Summary

This project presents a **conceptual six-layer architecture for an In-Vehicle Infotainment (IVI) system** integrating media playback, navigation, and smartphone projection.

The architecture uses concepts including:

**Binder IPC · AIDL · Car Service · Vehicle HAL · GNSS HAL · Audio HAL · Audio Focus · CAN · USB · Wi-Fi**

to explain how different components of a modern automotive infotainment system communicate.

The resulting model provides a structured foundation for understanding how IVI systems separate:

**Applications → Services → Automotive Logic → Hardware Abstraction → Physical Devices**

while maintaining clear interfaces, data flows, and separation between application functionality and vehicle hardware.
