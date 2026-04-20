# Baseband Module (0–2.9 GHz)

The Baseband Module provides the IF output of the HMC219 downconverter and prepares
the signal for the 6 cm and 3 cm upconverter modules. It includes filtering,
amplification, level control, and SMA interfacing.

---

## Purpose

- Accept IF output from the HMC219 mixer (0–2.9 GHz)
- Apply low‑pass filtering to remove mixer products
- Provide controlled gain and flatness across the band
- Deliver a clean IF signal to the 6 cm and 3 cm modules

---

## Block Diagram

HMC219 IF Out → Pad → IF Amplifier → 3 GHz LPF → Output Pad → SMA Out


---

## Gain Budget

See:  
`docs/rf-design/gain-budgets/baseband.md`

---

## Design Notes

- LPF cutoff: 3 GHz  
- Target output level: ~0 dBm  
- Short RF paths and solid ground stitching  
- SMA connectors for all I/O  
- Designed for KiCad 7+

---

## Test Plan

1. Verify LPF response (S21, S11)
2. Measure gain and flatness across 0–3 GHz
3. Confirm output level at nominal input
4. Check stability and compression
5. Validate isolation and return loss

---

## File Structure

baseband/
├── schematic.pdf
├── pcb/
├── bom.csv
└── readme.md

