# Base‑Band Tracking Generator Module  
**RF Output:** 0–2.9 GHz  
**Mixer Core:** HMC219  
**TG LO:** MAX2870 at fixed 3.9107 GHz  
**Reference:** 10 MHz from spectrum analyzer  
**Sweep Source:** 1st LO from spectrum analyzer

---

## Overview

The Base‑Band module generates a tracking‑synchronous RF output from 0–2.9 GHz.  
It is used for:

- IF chains up to S‑band  
- Wideband filters  
- General‑purpose stimulus in the 0–2.9 GHz range

The module uses:

- The **1st LO from the spectrum analyzer** as the swept input  
- A **fixed MAX2870 PLL** at \( 3.9107\ \text{GHz} \)  
- An **HMC219 mixer**  
- A **3 GHz low‑pass filter** and output pad

The mixer difference product reconstructs the analyzer RF at base‑band.

---

## Frequency plan

### Analyzer RF

The analyzer RF span for this module is:

\( RF_{SA} = 0\text{–}2.9\ \text{GHz} \)

### 1st LO from analyzer

The analyzer 1st LO is:

\( LO_{SA} = RF_{SA} + IF_1 \)

with

\( IF_1 = 3.9107\ \text{GHz} \)

So:

\( LO_{SA} = RF_{SA} + 3.9107\ \text{GHz} \)

Range:

\( LO_{SA} \approx 3.9\text{–}6.8\ \text{GHz} \)

### TG LO (MAX2870)

The MAX2870 is fixed at:

\( f_{\text{LO,TG}} = IF_1 = 3.9107\ \text{GHz} \)

### Mixer (HMC219)

The HMC219 uses:

- **RF port:** 1st LO from SA (after isolator and pad)  
- **LO port:** fixed \( f_{\text{LO,TG}} = 3.9107\ \text{GHz} \)  
- **IF port:** TG output (0–2.9 GHz)

Difference product:

\( IF_{TG} = RF_{\text{mixer}} - LO_{\text{mixer}} \)

With:

\( RF_{\text{mixer}} = LO_{SA} = RF_{SA} + IF_1 \)  
\( LO_{\text{mixer}} = f_{\text{LO,TG}} = IF_1 \)

So:

\( IF_{TG} = (RF_{SA} + IF_1) - IF_1 = RF_{SA} \)

Thus:

\( RF_{TG} = IF_{TG} = RF_{SA} = 0\text{–}2.9\ \text{GHz} \)

The TG output exactly tracks the analyzer RF in the 0–2.9 GHz range.

---

## Block diagram (text)

```
          ┌──────────────────────────────────────────────────────────┐
          │                      SA Interface                        │
          │  - 1st LO from SA: 3.9–6.8 GHz (+16.5 dBm)               │
          │  - 10 MHz reference (–10 dBm)                            │
          │  - SPI control from Raspberry Pi                         │
          └──────────────────────────────────────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │        Isolator          │
                     └──────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │         PAD –3 dB        │
                     └──────────────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC219 RF    │
                          │  (~12 dBm)     │
                          └────────────────┘
                                   ▲
                                   │
                     ┌──────────────────────────┐
                     │        PAD –6.5 dB       │
                     └──────────────────────────┘
                                   ▲
                                   │
                     ┌──────────────────────────┐
                     │ BPF 3.9107 GHz (–1.5 dB) │
                     └──────────────────────────┘
                                   ▲
                                   │
                     ┌──────────────────────────┐
                     │  Amp 3.9107 GHz (+20 dB) │
                     └──────────────────────────┘
                                   ▲
                                   │
                     ┌──────────────────────────┐
                     │    MAX2870 PLL (1 dBm)   │
                     │   fixed 3.9107 GHz LO    │
                     └──────────────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC219 LO    │
                          │  (~13 dBm)     │
                          └────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC219 IF    │
                          │  0–2.9 GHz     │
                          │  (~4 dBm)      │
                          └────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │    LPF 3 GHz (–1 dB)     │
                     └──────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │        PAD –3 dB         │
                     └──────────────────────────┘
                                   │
                                   ▼
                         TG Output 0–2.9 GHz (0 dBm)
```

---

## Power budget

### 1st LO path (RF into mixer)

- From SA 1st LO: 3.9–6.8 GHz, \( +16.5\ \text{dBm} \)  
- Isolator: ≈0 dB  
- PAD –3 dB: \( +13.5\ \text{dBm} \)  
- Into HMC219 RF: annotated as \( 12\ \text{dBm} \) (≈1.5 dB additional loss)

### TG LO path (MAX2870 → mixer LO)

- MAX2870 PLL: 3.9107 GHz, \( +1\ \text{dBm} \)  
- Amp 3.9107 GHz (+20 dB): \( +21\ \text{dBm} \)  
- BPF 3.9107 GHz (–1.5 dB): \( +19.5\ \text{dBm} \)  
- PAD –6.5 dB: \( +13\ \text{dBm} \)  
- Into HMC219 LO: \( \approx +13\ \text{dBm} \)

### IF / RF output chain

- HMC219 IF (0–2.9 GHz): \( \approx +4\ \text{dBm} \)  
- LPF 3 GHz (–1 dB): \( +3\ \text{dBm} \)  
- PAD –3 dB: \( 0\ \text{dBm} \) at TG output

---

## Control architecture

- MAX2870 referenced to the analyzer 10 MHz input  
- SPI control from Raspberry Pi (or MCU) to configure the fixed 3.9107 GHz LO  
- No sweep‑dependent tuning in this band—only band selection and LO enable/mute

---

## Mechanical / layout notes

- 3.9107 GHz LO chain (MAX2870 → amp → BPF → pad → HMC219 LO) short and well‑shielded.  
- Maintain isolation between the high‑level LO and the 0–2.9 GHz output.  
- FIne tune attentuator pads for: SA 1st LO, MAX2870 LO, HMC219 IF/RF, and final TG output.  
- 3 GHz LPF must have sufficient rejection of mixer images and LO feedthrough.

