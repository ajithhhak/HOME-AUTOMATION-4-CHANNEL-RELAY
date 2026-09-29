# 🏠 Smart Home Automation — Retrofit Controller

> A modular, low-cost smart home automation system designed to bring intelligent control to existing homes — without replacing traditional wall switches.

---

## 📌 Overview

This project began as a hobbyist smart home build and has evolved into a fully custom-designed, production-oriented PCB system.

**Core goal:** Add smart functionality to existing electrical wiring while keeping traditional physical switches fully operational.

The system is built around a **modular room-controller architecture** — each room hosts its own controller responsible for appliance control, physical switch inputs, environmental sensing, and presence detection.

The hardware has been developed end-to-end: from system architecture and component selection, through schematic capture, all the way to a complete 4-layer PCB layout in **Altium Designer**.

---

## ✨ Features

| Category | Detail |
|---|---|
| ⚡ Retrofit-friendly | Works with existing electrical installations |
| 🔌 Relay control | Controls lights, fans, and appliances |
| 🎛️ Physical switches | Wall switches remain fully functional |
| 👤 Presence detection | mmWave radar (HLK-LD2410C) |
| 🌡️ Environmental sensing | Temperature & humidity (SHT31) |
| 🚶 Motion detection | PIR sensor input |
| 🖥️ Local display | OLED status screen |
| 📡 Wireless | ESP32-based Wi-Fi connectivity |
| ☁️ IoT ready | Dashboard & cloud integration capable |
| 🔧 Modular | Room-level controller architecture |
| 🛡️ Isolation | Dedicated mains and SELV separation |
| 🔋 On-board PSU | Isolated AC-to-DC power supply |
| 📐 4-Layer PCB | Custom stackup with production-ready design |

---

## 🧠 System Architecture

```text
                    ┌─────────────────────────┐
                    │       AC MAINS          │
                    │      230V AC Input      │
                    └───────────┬─────────────┘
                                │
                         Protection & Filter
                                │
                    ┌───────────▼─────────────┐
                    │     Isolated AC/DC       │
                    │       HLK-10M05          │
                    └───────────┬─────────────┘
                                │ +5V
                         ┌──────▼──────┐
                         │  3.3V Power │
                         │   Regulator │
                         └──────┬──────┘
                                │
              ┌─────────────────▼─────────────────┐
              │             ESP32                 │
              │         Main Controller           │
              └──────┬──────────┬──────────┬──────┘
                     │          │          │
                ┌────▼────┐ ┌───▼────┐ ┌───▼────────┐
                │ ULN2803A│ │MCP23017│ │  Sensors   │
                │  Driver │ │ Switch │ │            │
                └────┬────┘ │ Inputs │ ├─ LD2410C  │
                     │      └────────┘ ├─ PIR      │
               ┌─────▼─────┐           ├─ SHT31    │
               │  Relays   │           └───────────┘
               └─────┬─────┘
                     │
              ┌──────▼──────┐
              │   Loads     │
              │Lights/Fans/ │
              │ Appliances  │
              └─────────────┘
```

---

## 🔩 Hardware

### Main Controller — ESP32-WROOM-32D

The ESP32 is the central processing unit and handles:
- Wi-Fi connectivity and IoT communication
- Relay switching logic
- I²C and UART peripheral communication
- Local automation and sensor processing

### Relay Driver — ULN2803A

A ULN2803A Darlington array drives the relay coils, keeping coil currents off the ESP32 GPIOs and providing integrated flyback suppression.

### Switch Input Expansion — MCP23017

An MCP23017 I²C GPIO expander provides additional inputs for up to **six physical wall switches**. The interface is fully **interrupt-driven** — no continuous polling.

### Presence Detection — HLK-LD2410C

The LD2410C mmWave radar sensor is connected via UART, providing rich presence data beyond a simple binary output — including stationary target detection.

### Environmental Sensor — SHT31

Provides accurate temperature and relative humidity measurements over I²C.

### Motion Detection

A PIR sensor provides a secondary motion detection channel for triggering automation logic.

---

## 🔌 Interfaces

### I²C Bus

```text
ESP32 GPIO21 → SDA
ESP32 GPIO22 → SCL

Peripherals: MCP23017 · SHT31 · OLED Display
```

### LD2410C UART

```text
LD2410C TX → ESP32 GPIO16 (RX)
LD2410C RX → ESP32 GPIO17 (TX)
```

### Relay Outputs

```text
ESP32 GPIO25 → ULN2803A → Relay 1
ESP32 GPIO26 → ULN2803A → Relay 2
ESP32 GPIO27 → ULN2803A → Relay 3
ESP32 GPIO33 → ULN2803A → Relay 4
```

Additional ULN2803A channels are available for future relay expansion.

---

## 🏗️ PCB Design

Designed entirely from scratch in **Altium Designer**.

### Layer Stackup

| Layer | Function |
|---|---|
| L1 — Top | Component placement, signal routing, mains routing |
| L2 | Solid GND copper pour |
| L3 | Power distribution / low-voltage routing |
| L4 — Bottom | Low-voltage signal routing |

### Design Methodology

Design rules were established **before routing** rather than applied as a cleanup step. Key areas of focus:

- Mains-to-SELV clearance and creepage distances
- High-current and mains track widths
- Solid ground connectivity and copper pour integrity
- Physical isolation boundary between mains and SELV sections
- Component placement for thermal and assembly considerations

The final DRC pass required only minor cleanup, with major electrical and clearance rules passing from early in the design cycle.

---

## ⚡ Power Architecture

The board uses a fully isolated AC/DC power supply architecture.

```text
230V AC
   │
   ▼
Fuse
   │
   ▼
MOV (Surge Protection)
   │
   ▼
X2 Safety Capacitor
   │
   ▼
Common-Mode Filter
   │
   ▼
HLK-10M05 (Isolated AC/DC)
   │
   ├──── +5V Rail
   │
   ▼
3.3V Buck Converter
   │
   └──── +3.3V Rail
```

The mains section is **electrically isolated** from the SELV control circuitry. Low-voltage electronics share a common DC ground on the secondary side only.

---

## 🏠 Retrofit Philosophy

A core design goal of this project is **retrofit compatibility** with existing residential electrical installations.

Traditional wall-switch operation is preserved at all times. The controller layers additional capabilities on top:

- Remote and voice control
- Presence and occupancy-based automation
- Environmental condition-based logic
- Room-level scheduling and scenes
- Future IoT platform integrations

This approach makes the system a suitable foundation for a **modular, scalable smart-home platform** that does not require rewiring or infrastructure changes.

---

## 🛠️ Design Workflow

```text
Concept & Requirements
        ↓
System Architecture
        ↓
Component Selection & Datasheet Research
        ↓
Schematic Capture
        ↓
Footprint Selection & Creation
        ↓
PCB Stackup & Design Rules
        ↓
Component Placement
        ↓
Routing
        ↓
Copper Pour & Ground Planes
        ↓
DRC & Clearance Verification
        ↓
Silkscreen & Documentation Cleanup
        ↓
Final PCB Output & Gerber Generation
```

One of the more time-intensive phases was **component selection and footprint validation** — not routing itself. Several footprints required manual creation or verification against mechanical drawings, checking pin spacing, hole dimensions, electrical ratings, and component availability before committing to the design.

---

## 📂 Repository Structure

```text
.
├── Hardware/
│   ├── Schematic/
│   ├── PCB/
│   ├── Footprints/
│   └── Documentation/
│
├── Firmware/
│
├── Software/
│
├── Images/
│
└── README.md
```

> Repository structure will evolve as the project progresses through fabrication and firmware development.

---

## 🚧 Current Status

### PCB Design — ✅ Complete

- [x] System architecture defined
- [x] Component selection finalised
- [x] Schematic completed
- [x] Custom footprints created and verified
- [x] PCB stackup configured
- [x] Component placement
- [x] Full board routing
- [x] Copper pours and ground planes
- [x] Mains/SELV isolation verified
- [x] DRC passed
- [x] Silkscreen and documentation cleanup
- [x] Gerber files generated

### Next Steps

- [ ] PCB fabrication
- [ ] PCB assembly
- [ ] Hardware bring-up and power verification
- [ ] Firmware development and integration
- [ ] Sensor validation
- [ ] Relay and load testing
- [ ] Thermal and power consumption testing
- [ ] Enclosure design and integration
- [ ] Long-term reliability testing

---

## ⚠️ Safety Notice

This project involves **230V AC mains voltage**.

The PCB contains both hazardous mains-level circuitry and low-voltage SELV electronics. Any fabrication, assembly, testing, or modification involving the mains section must be carried out following appropriate electrical safety procedures and applicable standards for PCB and product safety.

This is a development and learning project. It should not be treated as a certified commercial product without full safety, EMC, insulation, thermal, and regulatory validation.

---

## 👨‍💻 About This Project

**Smart Home Automation — Retrofit Controller**

Designed and developed as a personal electronics and embedded systems project, with the goal of evolving a hobbyist smart-home prototype into a clean, modular hardware platform.

**Tools & Technologies**

`Altium Designer` · `ESP32` · `C / C++` · `IoT` · `PCB Design` · `Embedded Systems`

---

## 📸 Project Photos & Renders

PCB renders, schematic screenshots, assembled board photos, and prototype images will be added as the project moves through fabrication and assembly.

---

## ⭐ From Prototype to Custom PCB

What started with an ESP32, a handful of sensors, some relays, and a tangle of jumper wires has become a fully custom-designed, production-oriented 4-layer PCB.

The next milestone:

> **CAD → Fabrication → Assembly → Bring-Up → Working Smart-Home Controller**
