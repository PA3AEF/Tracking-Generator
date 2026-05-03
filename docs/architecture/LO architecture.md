# LO Architecture  
**Tracking Generator for HP856x Spectrum Analyzers**

This document describes how each TG band derives its LO signals and how these relate to the analyzer’s internal LO scheme.  
The TG always reconstructs the analyzer’s RF using a combination of:

- The **1st LO from the analyzer**  
- A **band‑specific TG LO** (PLL or harmonic source)  
- A **mixer** (HMC219 or HMC220 depending on band)

The TG output always satisfies: `RF_TG = RF_SA`  where `RF_SA` is the analyzer’s swept RF.

---

# 1. Analyzer LO Background

The HP856x family uses a first IF of: `IF_1 = 3.9107 GHz`

The analyzer 1st LO is: `LO_SA = RF_SA + IF_1`

For higher bands the analyzer uses harmonic mixing, but the TG always reconstructs the *fundamental* RF.

---

# 2. Base‑Band LO Scheme (0–2.9 GHz)

**TG LO:** fixed MAX2870 at `3.9107 GHz`  
**Analyzer LO:** `LO_SA = RF_SA + 3.9107 GHz`  
**Mixer:** HMC219 (difference product)

### Frequency relationship

Mixer RF port receives: `LO_SA = RF_SA + IF_1`

Mixer LO port receives: `LO_TG = IF_1 = 3.9107 GHz`

Mixer IF output: `RF_TG = LO_SA - LO_TG = RF_SA`

### Summary

- No sweeping PLL  
- TG LO is fixed  
- Analyzer 1st LO provides the sweep  
- Output range: 0–2.9 GHz

---

# 3. 6 cm LO Scheme (approx. 4–8 GHz)

**TG LO:** MAX2870 (swept)  
**Analyzer LO:** harmonic mode `n = 2`  
**Mixer:** HMC219

### Analyzer LO

`LO_SA = (RF_SA + IF_1) / 2`

### TG LO

TG synthesizes: `LO_TG = LO_SA - RF_SA`

so that: `RF_TG = LO_SA - LO_TG = RF_SA`

### Summary

- TG PLL sweeps  
- Analyzer LO is halved fundamental  
- Mixer difference product reconstructs RF

---

# 4. 3 cm LO Scheme (9.5–11.5 GHz)

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

### Summary

- Analyzer LO is tripled‑mode  
- TG generates a low IF  
- Doubled analyzer LO provides the high LO  
- HMC220 difference product becomes the RF output

---

# 5. 15 mm LO Scheme (18–26 GHz)

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

### Summary

- Analyzer LO is sixth‑harmonic  
- TG generates swept IF  
- Multiplied analyzer LO provides high LO  
- HMC220 difference product reconstructs RF

---

# 6. Summary

| Band | Output Range | Analyzer LO Mode | TG LO | Mixer | Relationship |
|------|--------------|------------------|-------|--------|--------------|
| Base‑Band | 0–2.9 GHz | Fundamental | MAX2870 at 3.9107 GHz | HMC219 | `RF_TG = LO_SA - 3.9107` |
| 6 cm | ~4–8 GHz | `n = 2` | Swept MAX2870 | HMC219 | `RF_TG = LO_SA - LO_TG` |
| 3 cm | 9.5–11.5 GHz | `n = 3` | Swept ADF4351 IF | HMC220 | `RF_TG = LO_2 - IF_TG` |
| 15 mm | 18–26 GHz | `n = 6` | Swept ADF4351 IF | HMC220 | `RF_TG = LO_6 - IF_TG` |

---

# 7. Design Philosophy

- The TG always reconstructs the analyzer’s **fundamental RF**, regardless of harmonic mode.  
- The analyzer LO provides the **sweep timing**.  
- The TG LO provides the **offset** needed to collapse the analyzer LO back to the fundamental.  
- Mixers always operate in **difference mode** for stability and predictable levels.  
- LO multiplication (×2, ×6) is always done on the analyzer LO, never on the TG LO.
