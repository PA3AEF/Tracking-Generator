# 6 cm Module (4.3–7.2 GHz)

The 6 cm Module upconverts the baseband IF signal to the 4.3–7.2 GHz band using
an HMC220 mixer, RF amplification, and band‑pass filtering.

---

## Purpose

- Upconvert IF (0–2.9 GHz) to 6 cm RF band
- Provide clean, stable output with controlled gain
- Filter LO and IF leakage
- Interface via SMA connectors

---

## Block Diagram

IF In → Pad → HMC220 Mixer → BPF (4–8 GHz) → RF Amplifier → Output Pad → SMA Out


---

## Gain Budget

See:  
`docs/rf-design/gain-budgets/6cm.md`

---

## Design Notes

- Mixer: HMC220  
- LO frequency: 4.3 GHz  
- RF band: 4.3–7.2 GHz  
- BPF: 4–8 GHz SMA module  
- Output target: ~0 dBm  
- Ensure good grounding and isolation between IF/LO/RF paths

---

## Test Plan

1. Verify mixer conversion loss
2. Measure RF output across full band
3. Check LO leakage and suppression
4. Validate BPF response
5. Confirm gain and compression point
6. Evaluate noise floor and spurious content

---

## File Structure

6cm/
├── schematic.pdf
├── pcb/
├── bom.csv
└── readme.md

