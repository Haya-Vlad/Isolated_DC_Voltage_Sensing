# Isolated DC Voltage Sensing

A fully isolated DC bus voltage sensing circuit designed for power electronics applications, capable of sensing **0–300 V DC** and scaling the signal to a **0–3.3 V ADC range** using the AMC1200 isolation amplifier.



## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Circuit Architecture](#circuit-architecture)
- [Signal Chain](#signal-chain)
- [Components](#components)
- [Repository Structure](#repository-structure)
- [Hardware Design](#hardware-design)
- [Power Supply](#power-supply)
- [Getting Started](#getting-started)
- [Bill of Materials](#bill-of-materials)
- [License](#license)

---

## Overview

This project implements a  isolated DC voltage sensing solution for high-voltage DC bus measurements (up to 300 V). The circuit uses the **AMC1200** precision isolation amplifier to safely measure high-voltage DC signals while maintaining full electrical isolation between the high-voltage side and the low-voltage (microcontroller/ADC) side.

The design is suitable for use in:
- Motor drives and inverters
- Battery management systems (BMS)
- Solar/PV converters
- DC-DC converters
- Any power electronics system requiring isolated voltage feedback

---

## Features

-  **Input range:** 0–300 V DC
-  **Output range:** 0–3.3 V (direct ADC-compatible)
-  **Full galvanic isolation** via AMC1200
-  **Anti-aliasing filter** at ADC input (150 Ω + 1 nF)
-  **RC pre-filter** for noise reduction before AMC1200
-  **Dual isolated power supply** using LD1117 and L7805 regulators
-  **Unipolar signal path** — no DC offset reference required
-  **MCP6002 op-amp** for output signal conditioning
-  **Designed in Eagle CAD**, Gerber files included
-  **Practically implemented and tested**

---

## Circuit Architecture

```
DC Bus (0–300V)
      │
      ▼
┌─────────────────────┐
│  Resistor Divider   │  620kΩ × 3 on each rail
│  (High-side input   │  which scales to ±250mV differential
│   attenuation)      │
└────────┬────────────┘
         │ Differential ±250mV
         ▼
┌─────────────────────┐
│      AMC1200        │  Isolation amplifier
│  (Isolation Stage)  │  Gain = 8 (±250mV in → ±2V out)
│                     │  Galvanic isolation barrier
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│   RC Filter Stage   │  Initial noise suppression
│  (12Ω + 330pF)      │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│      MCP6002        │  Signal conditioning op-amp
│  (Output Scaling)   │  Scales to 0–3.3V ADC range
│                     │  10kΩ feedback network
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Anti-Aliasing      │  150Ω + 1nF
│  Filter             │  High-frequency noise rejection
└────────┬────────────┘
         │
         ▼
    OUTPUT_TO_ADC (0–3.3V)
```

---

## Signal Chain

1. **Input Attenuation:** The DC bus voltage (0–300 V) is divided by a high-value resistor network  to produce a small differential voltage within the AMC1200's ±250 mV input range.

2. **Isolation (AMC1200):** The AMC1200 isolation amplifier provides galvanic isolation with a fixed gain of 8. The ±250 mV differential input is amplified and transferred across the isolation barrier.

3. **RC Pre-filter:** A 12 Ω + 330 pF RC filter stage provides initial high-frequency noise attenuation immediately after the AMC1200 output.

4. **Signal Conditioning (MCP6002):** The MCP6002 op-amp is configured as a scaling amplifier with a 10 kΩ feedback network. Since the DC bus signal is inherently unipolar, no DC offset reference is required so the signal maps directly into the 0–3.3 V ADC range.

5. **Anti-Aliasing Filter:** A 150 Ω + 1 nF RC filter at the ADC input provides additional high-frequency noise suppression and prevents aliasing during ADC sampling.

---

## Components

| Reference | Component | Value/Part | Description |
|-----------|-----------|------------|-------------|
| U32 | AMC1200 | AMC1200 | Precision isolation amplifier |
| U37 | MCP6002 | MCP6002 | Dual rail-to-rail op-amp |
| CR2 | LD1117 | LD1117 | LDO voltage regulator (isolated side) |
| CR5 | L7805 | L7805 | 5V linear regulator |
| D3, D4 | HSMG-C170 | HSMG-C170 | Green & Red status LED |
| R177–R179, R183–R187 | Resistor | 620 kΩ | Input voltage divider |
| R170, R172–R173 | Resistor | 12 Ω | RC filter resistors |
| R174–R175 | Resistor | 10 kΩ | Op-amp feedback network |
| R259 | Resistor | 150 Ω | Anti-aliasing filter |
| R257, R258 | Resistor | 510 Ω | LED current limiting |
| C122, C125 | Capacitor | 330 pF | RC filter capacitors |
| C126, C127 | Capacitor | 0.1 µF | Decoupling capacitors |
| C153, C155 | Capacitor | 470 µF | Bulk filtering |
| C157, C161–C162 | Capacitor | 100 nF | Supply decoupling |
| C158 | Capacitor | 1 nF | Anti-aliasing filter capacitor |
| L3 | Ferrite Beads | 32mohms | Power supply filter |

---

## Repository Structure

```
isolated-dc-voltage-sensing/
│
├── README.md                  ← You are here
├── LICENSE
├── CHANGELOG.md
│
├── hardware/
│   ├── eagle/
│   │   ├── schematic/         ← .sch Eagle schematic files
│   │   ├── board/             ← .brd Eagle board layout files
│   │   └── libraries/         ← Custom Eagle component libraries (.lbr)
│   ├── gerber/                ← Fabrication-ready Gerber files
│   │   ├── *.GTL              (Top copper)
│   │   ├── *.GBL              (Bottom copper)
│   │   ├── *.GTS              (Top soldermask)
│   │   ├── *.GBS              (Bottom soldermask)
│   │   ├── *.GTO              (Top silkscreen)
│   │   ├── *.DRL              (Drill file)
│   │   └── gerber_readme.md
│   │
│── docs/
    ├── design_notes.md        ← Design decisions and calculations
    ├── signal_chain.md        ← Detailed signal chain analysis
    └── datasheets/            ← Key component datasheets (PDFs)
        ├── AMC1200.pdf
        ├── MCP6002.pdf
        ├── LD1117.pdf
        └── L7805.pdf
```

---

## Hardware Design

The design was created in **Autodesk Eagle CAD**. All source files are located under `hardware/eagle/`.

- **Schematic:** `hardware/eagle/schematic/`
- **Board layout:** `hardware/eagle/board/`
- **Gerber files** (ready for PCB fabrication): `hardware/gerber/`

### Power Supply Architecture

The circuit uses a **dual isolated power supply**:
- **VCC_A / GND_A** — High-voltage (DC bus) side supply, regulated via LD1117
- **VCC_B / GND_B** — Low-voltage (MCU/ADC) side supply, regulated via L7805
- **VCC_12V_A+ / VCC_12V_B+** — 12V rails feeding each regulator

The dual-supply architecture ensures that the isolation barrier of the AMC1200 is maintained across both the signal path and the power supply.

---

## Getting Started

### PCB Fabrication
1. Download the Gerber files from `hardware/gerber/`
2. Upload to your preferred PCB manufacturer (JLCPCB, PCBWay, OSHPark, etc.)
3. Recommended specs: 2-layer, 1.6 mm FR4, HASL or ENIG finish

### Assembly
1. Refer to `hardware/bom/bom.csv` for component sourcing
2. Follow the schematic in `hardware/eagle/schematic/`
3. Pay attention to orientation of AMC1200 and polarized capacitors

### Testing
1. Apply a known DC voltage of 12V to DC_A / DC_B inputs
2. Measure OUTPUT_TO_ADC with a multimeter or oscilloscope
3. Verify scaling linearity against expected values 

---

## Bill of Materials

See [`hardware/bom`](hardware/bom.pdf) for the complete BOM.

---

## License

This project is released under the [MIT License](LICENSE).

---

## Author

**Aditya Gohil**  
Power Electronics Design  
*Designed and practically implemented — May 2026*
