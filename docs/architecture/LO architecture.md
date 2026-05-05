
---

# LO Architecture  
**Tracking Generator for HP856x Spectrum Analyzers**

This document describes how each TG band derives its LO signals and how these relate to the analyzer’s internal LO scheme.  
The TG always reconstructs the analyzer’s RF using a combination of:

- The **1st LO from the analyzer**  
- A **band‑specific TG LO** (PLL or harmonic source)  
- A **mixer** (HMC219 or HMC220 depending on band)

The TG output always satisfies: `RF_TG = RF_SA`  where `RF_SA` is the analyzer’s swept RF.

---

## Global LO Distribution

When all TG modules (Base‑Band, 6 cm, 3 cm, 15 mm) are installed, the spectrum analyzer provides a single LO signal:

- `SA_LO = 3.9–6.8 GHz`  
- Level: `+16.5 dBm`

To supply this LO to all modules simultaneously while protecting the analyzer and maintaining correct drive levels, the TG uses a centralized LO distribution network.  
This network consists of:

- One **broadband isolator** directly at the SA_LO input  
- A **fixed pad** for level conditioning  
- A **broadband LO buffer amplifier**  
- A **4‑way Wilkinson splitter** feeding the four TG modules

This arrangement provides:

- Analyzer protection (isolator)  
- Reverse isolation between modules (amp + splitter)  
- Consistent LO levels to each module  
- Identical behavior to direct front‑panel patching

### LO Distribution Architecture

```
SA_LO (+16.5 dBm)
        │
        ▼
Broadband Isolator  (≈ –1 dB)
        │
        ▼
Pad –3 dB
        │
        ▼
Broadband LO Buffer Amp (+10 dB)
        │
        ▼
4‑Way Wilkinson Splitter (–7 dB total)
        │
   ┌────┼────┬────┐
   ▼    ▼    ▼    ▼
Base  6cm  3cm  15mm
Module Module Module Module
```

Each module receives approximately the same LO level as if it were connected directly to the analyzer.

### Level Budget

| Stage | Frequency | Level |
|-------|-----------|--------|
| SA_LO output | 3.9–6.8 GHz | +16.5 dBm |
| Isolator | 3.9–6.8 GHz | +15.5 dBm |
| Pad –3 dB | 3.9–6.8 GHz | +12.5 dBm |
| LO buffer amp (+10 dB) | 3.9–6.8 GHz | +22.5 dBm |
| 4‑way splitter (–7 dB) | 3.9–6.8 GHz | +15.5 dBm per port |
| Module LO input | 3.9–6.8 GHz | +15 to +16 dBm |

### Rationale

- The isolator protects the analyzer and defines a clean source impedance.  
- The buffer amplifier provides reverse isolation and compensates for splitter loss.  
- The 4‑way splitter ensures equal LO drive to all modules.  
- Each module continues to use its own pads, isolators, doublers, and LO amplifiers exactly as originally designed.

This approach eliminates the need for front‑panel LO patching while maintaining full electrical compatibility with all TG modules.

---

## Analyzer LO Background

The HP856x family uses a first IF of: `IF_1 = 3.9107 GHz`

The analyzer 1st LO is: `LO_SA = RF_SA + IF_1`

For higher bands the analyzer uses harmonic mixing, but the TG always reconstructs the *fundamental* RF.

---

## Base‑Band LO Scheme (0–2.9 GHz)

**TG LO:** fixed MAX2870 at `3.9107 GHz`  
**Analyzer LO:** `LO_SA = RF_SA + 3.9107 GHz`  
**Mixer:** HMC219 (difference product)

### Frequency relationship

Mixer RF port receives: `LO_SA = RF_SA + IF_1`

Mixer LO port receives: `LO_TG = IF_1 = 3.9107 GHz`

Mixer IF output: `RF_TG = LO_SA - LO_TG = RF_SA`

### Summary for Base-Band module

- No sweeping PLL  
- TG LO is fixed  
- Analyzer 1st LO provides the sweep  
- Output range: 0–2.9 GHz

---

## 6 cm LO Scheme (approx. 4–8 GHz)

**TG LO:** MAX2870 (swept)  
**Analyzer LO:** harmonic mode `n = 2`  
**Mixer:** HMC219

### Analyzer LO

`LO_SA = (RF_SA + IF_1) / 2`

### TG LO

TG synthesizes: `LO_TG = LO_SA - RF_SA`

so that: `RF_TG = LO_SA - LO_TG = RF_SA`

### Summary for 6 cm module

- TG PLL sweeps  
- Analyzer LO is halved fundamental  
- Mixer difference product reconstructs RF

---

## 3 cm LO Scheme (9.5–11.5 GHz)

**TG LO:** ADF4351 (IF LO, swept)  
**Analyzer LO:** harmonic mode `n = 3`  
**Mixer:** HMC220  
**LO2:** doubled analyzer LO

### Analyzer LO

`LO_SA = (RF_SA + IF_1) / 3`

Doubled LO: `LO_2 = 2 * LO_SA`

### TG IF LO

TG synthesizes: `IF_TG = (2 * IF_1 - RF_SA) / 3`

### Mixer output

`RF_TG = LO_2 - IF_TG = RF_SA`

### Summary for 3 cm module

- Analyzer LO is tripled‑mode  
- TG generates a low IF  
- Doubled analyzer LO provides the high LO  
- HMC220 difference product becomes the RF output

---

## 15 mm LO Scheme (18–26 GHz)

**TG LO:** ADF4351 (IF LO, swept)  
**Analyzer LO:** harmonic mode `n = 6`  
**Mixer:** HMC220  
**LO6:** multiplied analyzer LO

### Analyzer LO

`LO_SA = (RF_SA + IF_1) / 6`

Multiplied LO: `LO_6 = 6 * LO_SA`

### TG IF LO

TG synthesizes: `IF_TG = LO_6 - RF_SA`

### Mixer output

`RF_TG = LO_6 - IF_TG = RF_SA`

## Summary for 15 mm module

- Analyzer LO is sixth‑harmonic  
- TG generates swept IF  
- Multiplied analyzer LO provides high LO  
- HMC220 difference product reconstructs RF

---

# Summary

| Band | Output Range | Analyzer LO Mode | TG LO | Mixer | Relationship |
|------|--------------|------------------|-------|--------|--------------|
| Base‑Band | 0–2.9 GHz | Fundamental | MAX2870 at 3.9107 GHz | HMC219 | `RF_TG = LO_SA - 3.9107` |
| 6 cm | ~4–8 GHz | `n = 2` | Swept MAX2870 | HMC219 | `RF_TG = LO_SA - LO_TG` |
| 3 cm | 9.5–11.5 GHz | `n = 3` | Swept ADF4351 IF | HMC220 | `RF_TG = LO_2 - IF_TG` |
| 15 mm | 18–26 GHz | `n = 6` | Swept ADF4351 IF | HMC220 | `RF_TG = LO_6 - IF_TG` |



---

# Design Philosophy

- The TG always reconstructs the analyzer’s **fundamental RF**, regardless of harmonic mode.  
- The analyzer LO provides the **sweep timing**.  
- The TG LO provides the **offset** needed to collapse the analyzer LO back to the fundamental.  
- Mixers always operate in **difference mode** for stability and predictable levels.  
- LO multiplication (×2, ×6) is always done on the analyzer LO, never on the TG LO.
