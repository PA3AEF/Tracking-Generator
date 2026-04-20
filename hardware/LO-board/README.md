# LO Board — Central PLL System

The LO Board generates all local oscillator signals required by the TG system.
It includes three PLLs, LO amplification, band‑pass filtering, and a 10 MHz
reference input.

---

## Purpose

- Generate stable LO signals for all RF modules:
  - 3.9107 GHz (baseband)
  - 4.3 GHz (6 cm)
  - 8.8 GHz (3 cm)
- Filter and amplify each LO to the required mixer drive level
- Provide SPI control interface
- Accept external 10 MHz reference

---

## Block Diagram

10 MHz Ref → PLLs → LO Amplifiers → SMA BPFs → SMA Outputs


---

## LO Chains

Detailed LO chain documentation:  
- **`docs/rf-design/lo-chains/`**

---

## Design Notes

- LO drive target: +12 to +13 dBm at mixer input  
- SMA BPFs used to clean up PLL and amplifier harmonics  
- SPI control via Raspberry Pi or microcontroller  
- Ensure isolation between LO paths  
- Solid ground stitching and short RF traces

---

## Test Plan

1. Verify PLL lock and frequency accuracy
2. Measure LO phase noise
3. Check LO output power after filtering
4. Validate harmonic suppression
5. Confirm stability across temperature
6. Test reference input operation

---
