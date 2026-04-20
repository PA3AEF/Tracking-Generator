# 3 cm Module (8.8–11.7 GHz)

The 3 cm Module upconverts the baseband IF signal to the 8.8–11.7 GHz band using
an HMC220 mixer, high‑frequency RF amplification, and band‑pass filtering.

---

## Purpose

- Upconvert IF (0–2.9 GHz) to the 3 cm RF band
- Provide clean, stable output with controlled gain
- Suppress LO and IF leakage
- Interface via SMA connectors

---

## Block Diagram

IF In → Pad → HMC220 Mixer → BPF (8–12 GHz) → RF Amplifier → Output Pad → SMA Out


---

## Gain Budget

See:  
- **`docs/rf-design/gain-budgets/3cm.md`**

---

## Design Notes

- Mixer: HMC220  
- LO frequency: 8.8 GHz  
- RF band: 8.8–11.7 GHz  
- BPF: 8–12 GHz SMA module  
- Output target: ~0 dBm  
- Pay attention to RF layout at >10 GHz (short traces, via fences)

---

## Test Plan

1. Verify mixer conversion loss at 9–12 GHz
2. Measure RF output across full band
3. Check LO leakage and suppression
4. Validate BPF response
5. Confirm gain and compression point
6. Evaluate spurious and harmonic content

---



