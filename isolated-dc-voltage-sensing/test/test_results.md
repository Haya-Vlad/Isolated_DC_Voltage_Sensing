# Test Results — Isolated DC Voltage Sensing

**Tester:** Aditya Gohil  
**Date:** May 2026  
**Board Version:** v1.0

---

## Test Equipment Used

| Equipment | Model | Notes |
|-----------|-------|-------|
| DC Power Supply | — | Variable 0–300V |
| Multimeter | — | For OUTPUT_TO_ADC measurement |
| Oscilloscope | — | Noise / ripple observation |
| Logic Analyzer / MCU | — | ADC reading verification |

---

## Linearity Test — OUTPUT_TO_ADC vs Input Voltage

| DC Bus Input (V) | Expected ADC Output (V) | Measured ADC Output (V) | Error (%) |
|------------------|-------------------------|--------------------------|-----------|
| 0                | 0.000                   |                          |           |
| 30               | 0.330                   |                          |           |
| 60               | 0.660                   |                          |           |
| 100              | 1.100                   |                          |           |
| 150              | 1.650                   |                          |           |
| 200              | 2.200                   |                          |           |
| 250              | 2.750                   |                          |           |
| 300              | 3.300                   |                          |           |

*Fill in measured values during hardware testing.*

---

## Isolation Verification

- [ ] Confirm no DC continuity between GND_A and GND_B
- [ ] Confirm no DC continuity between DC bus input rails and OUTPUT_TO_ADC
- [ ] Hipot test (if applicable): ______ V applied for ______ seconds — Pass / Fail

---

## Noise / Ripple Measurement

| Condition | Frequency | Peak-to-Peak Noise (mV) | Notes |
|-----------|-----------|--------------------------|-------|
| DC bus = 100V, no load | — | | |
| DC bus = 100V, switching load | — | | |

---

## Power Supply Rails

| Rail | Expected (V) | Measured (V) | Pass/Fail |
|------|-------------|--------------|-----------|
| VCC_12V_A+ (input) | 12.0 | | |
| VCC_A (LD1117 output) | | | |
| VCC_12V_B+ (input) | 12.0 | | |
| VCC_B (L7805 output) | 5.0 | | |

---

## Notes

*Add any observations, anomalies, or rework performed during testing here.*
