# Design Notes — Isolated DC Voltage Sensing

**Author:** Aditya Gohil  
**Date:** May 2026  
**Tool:** Autodesk Eagle CAD

---

## 1. Design Goals

- Measure DC bus voltages in the range **0–300 V**
- Provide **galvanic isolation** between high-voltage and low-voltage domains
- Output a signal in the **0–3.3 V range** suitable for direct ADC sampling
- Minimize noise at the ADC input
- Keep the design compact and cost-effective

---

## 2. Component Selection

### AMC1200 — Isolation Amplifier
The AMC1200 was chosen for its:
- Fixed gain of 8 with ±250 mV differential input range
- High isolation voltage (up to 4250 V peak)
- Low offset voltage and excellent linearity
- SPI-free, analog-in/analog-out interface — no firmware dependency

### MCP6002 — Signal Conditioning Op-Amp
The MCP6002 was selected for its:
- Rail-to-rail input/output capability
- Single-supply operation (3.3V or 5V compatible)
- Low cost, widely available dual op-amp package
- Sufficient bandwidth for DC sensing applications

### LD1117 / L7805 — Voltage Regulators
- **LD1117 (ADJ):** Used on the isolated (high-voltage) side to supply the AMC1200 VDD
- **L7805:** Used on the low-voltage side to supply the MCP6002 and output circuitry

---

## 3. Input Attenuation Network

### Resistor Divider Calculation

The DC bus voltage (0–300 V) must be scaled to the AMC1200's ±250 mV differential input range.

Each leg of the differential input uses **3 × 620 kΩ resistors in series = 1.86 MΩ**.

A **2.2 kΩ** differential sense resistor is placed between the two legs.

Differential voltage across sense resistor:
```
V_diff = V_bus × (R_sense / (2 × R_series + R_sense))
V_diff = 300 × (2200 / (2 × 1,860,000 + 2200))
V_diff ≈ 300 × (2200 / 3,722,200)
V_diff ≈ 300 × 0.000591
V_diff ≈ 177 mV  (well within ±250 mV limit)
```

This gives comfortable headroom within the AMC1200 input range.

---

## 4. AMC1200 Output Scaling

The AMC1200 has a fixed gain of **8**.

Output voltage from AMC1200 for max input (±250 mV):
```
V_out_AMC = ±250 mV × 8 = ±2 V
```

Since the DC bus signal is unipolar (0–300 V), the output of the AMC1200 is also unipolar (0 to ~1.4 V at 300 V input given the actual divider ratio).

---

## 5. MCP6002 Gain Stage

The MCP6002 is configured as a non-inverting amplifier to scale the AMC1200 output to the 0–3.3 V ADC range.

Gain is set by the 10 kΩ feedback resistors.

Since the signal is unipolar, no virtual ground or DC offset reference is needed — the signal naturally maps into the positive ADC range.

---

## 6. Filtering Strategy

### Stage 1 — RC Pre-filter (after AMC1200 output)
- **R = 12 Ω, C = 330 pF**
- Cut-off frequency: `f_c = 1 / (2π × 12 × 330×10⁻¹²) ≈ 40 MHz`
- Provides initial HF noise damping

### Stage 2 — Anti-Aliasing Filter (at ADC input)
- **R = 150 Ω, C = 1 nF**
- Cut-off frequency: `f_c = 1 / (2π × 150 × 1×10⁻⁹) ≈ 1.06 MHz`
- Prevents aliasing during ADC sampling
- This is the dominant low-pass filter in the signal chain

---

## 7. Isolation Barrier Considerations

The AMC1200 provides isolation between:
- **VCC_A / GND_A** domain (connected to DC bus side)
- **VCC_B / GND_B** domain (connected to MCU/ADC side)

Both power domains are independently regulated. The 12V input rails (VCC_12V_A+ and VCC_12V_B+) must themselves come from an isolated DC-DC converter upstream, or from separate transformer-derived supplies, to maintain the full isolation chain.

---

## 8. Known Limitations

- The resistor divider introduces some power dissipation at high bus voltages (~24 mW at 300 V) — acceptable for most applications
- The AMC1200 output is referenced to the isolated GND_A; level shifting is handled by the dual-supply scheme
- Accuracy is limited by resistor tolerance of the divider network — use 1% or 0.1% resistors for best precision
