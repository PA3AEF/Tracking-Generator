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

- The 1st LO from the spectrum analyzer as the swept input  
- A fixed MAX2870 PLL at 3.9107 GHz  
- An HMC219 mixer  
- A 3 GHz low‑pass filter and output pad

The mixer’s difference product reconstructs the analyzer RF at base‑band.

---

## Frequency plan

### Analyzer RF

The analyzer RF span for this module is: `RF_SA = 0 to 2.9 GHz`

### 1st LO from analyzer

The analyzer 1st LO is defined as: `LO_SA = RF_SA + IF1`

with: `IF1 = 3.9107 GHz`

So the analyzer LO range becomes: `LO_SA = 3.9107 GHz to 6.8107 GHz`

### TG LO (MAX2870)

The MAX2870 is programmed once at power‑up to: `LO_TG = 3.9107 GHz`

### Mixer (HMC219)

The HMC219 uses:

- RF port: analyzer 1st LO (after isolator and pad)  
- LO port: fixed 3.9107 GHz  
- IF port: TG output (0–2.9 GHz)

Mixer output is the difference: `IF_TG = LO_SA - LO_TG`

Substituting:

- `IF_TG = (RF_SA + 3.9107 GHz) - 3.9107 GHz`  
- `IF_TG = RF_SA`

Thus the TG output equals the analyzer RF: `RF_TG = RF_SA`

---

## Block diagram

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

- SA 1st LO: 3.9–6.8 GHz at +16.5 dBm  
- Isolator: ~0 dB  
- Pad –3 dB → +13.5 dBm  
- Additional small losses → ~12 dBm into HMC219 RF port

### TG LO path (MAX2870 → mixer LO)

- MAX2870 output: +1 dBm  
- Amplifier +20 dB → +21 dBm  
- BPF –1.5 dB → +19.5 dBm  
- Pad –6.5 dB → +13 dBm into HMC219 LO port

### IF / RF output chain

- HMC219 IF output: ~+4 dBm  
- LPF –1 dB → +3 dBm  
- Pad –3 dB → 0 dBm final TG output

---

## Control architecture

- MAX2870 uses the analyzer’s 10 MHz reference  
- SPI control from Raspberry Pi (or MCU)  
- LO is fixed; no sweep‑dependent tuning  
- Only band selection and LO enable/mute are controlled

---

## Mechanical / layout notes

- 3.9107 GHz LO chain short and well‑shielded  
- Isolation between the high‑level LO and the 0–2.9 GHz output  
- FIne tune attenuation pads for correct level at SA 1st LO, MAX2870 LO, HMC219 IF/RF, and final output  
- 3 GHz LPF to sufficiently suppress mixer images and LO feedthrough
