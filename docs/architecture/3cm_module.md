# 3 cm Tracking Generator Module  
**RF Output:** 9.5–11.5 GHz (2 GHz span)  
**Harmonic Mode:** \( n = 3 \)  
**Mixer Core:** HMC220  
**TG LO:** ADF4351 (0.56–1.23 GHz)  
**SA LO Path:** Doubled \( LO_{SA} \) (8.94–10.28 GHz)

---

## Overview

The 3 cm module generates a tracking‑synchronous RF output in the 9.5–11.5 GHz range.  
It follows the analyzer’s 3× harmonic mixing mode and derives all timing from:

- \( V_{\text{sweep}} \)  
- Analyzer \( LO_{SA} \) (fundamental)  
- 10 MHz reference

The module uses a doubled analyzer LO as the high‑frequency LO for the mixer and an ADF4351 as the low‑frequency IF LO.  
The HMC220 produces the final RF output via the difference product.

---

## Frequency plan

### Analyzer‑derived quantities

\( RF_{SA} = 2 \cdot V_{\text{sweep}} \)  

\( IF_1 = 3.9107\ \text{GHz} \)

### Analyzer LO (fundamental)

\( LO_{SA} = \dfrac{RF_{SA} + IF_1}{3} \)

### Doubled LO for HMC220 LO port

\( LO_2 = 2 \cdot LO_{SA} \)

For \( RF_{SA} = 9.5\text{–}11.5\ \text{GHz} \) &rarr;  \( LO_2 \approx 8.94\text{–}10.28\ \text{GHz} \)

### TG IF LO (ADF4351)

The mixer uses the difference product \( RF_{TG} = LO_2 - IF_{TG} \).  

Tracking requires \( RF_{TG} = RF_{SA} \).

Therefore \( IF_{TG} = LO_2 - RF_{SA} \).

Substituting \( LO_2 \) &rarr;    \( IF_{TG} = \dfrac{2(RF_{SA} + IF_1)}{3} - RF_{SA} \)

Simplified &rarr;  \( IF_{TG} = \dfrac{2IF_1 - RF_{SA}}{3} \)

Range &rarr;  \( IF_{TG} \approx 0.56\text{–}1.23\ \text{GHz} \)

This is the ADF4351 output frequency.

---

## Block diagram

```
          ┌──────────────────────────────────────────────────────────┐
          │                      SA Interface                        │
          │     - V_sweep (DC)                                       │
          │     - LO_SA fundamental (4.47–5.14 GHz)                  │
          │     - 10 MHz reference                                   │
          └──────────────────────────────────────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │  LO_SA Buffer / Limiter  │
                     └──────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │     Frequency Doubler    │
                     │   (→ 8.94–10.28 GHz)     │
                     └──────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │     LO Amplifier         │
                     │     (+7 dBm target)      │
                     └──────────────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC220 LO    │
                          └────────────────┘
                                   ▲
                                   │
                     ┌──────────────────────────┐
                     │      ADF4351 PLL/VCO     │
                     │   (0.56–1.23 GHz IF_TG)  │
                     └──────────────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC220 IF    │
                          └────────────────┘
                                   │
                                   ▼
                          ┌────────────────┐
                          │   HMC220 RF    │
                          └────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │     RF Amplifier (+15)   │
                     └──────────────────────────┘
                                   │
                                   ▼
                     ┌──────────────────────────┐
                     │  Flattening / Output Pad │
                     │     (0 dBm nominal)      │
                     └──────────────────────────┘
                                   │
                                   ▼
                         TG Output (9.5–11.5 GHz)
```

---

## Power budget (nominal)

| Stage | Frequency | Level |
|-------|-----------|--------|
| SA_LO input | 4.47–5.14 GHz | ~0 dBm |
| Doubler output | 8.94–10.28 GHz | –6 dBm |
| LO amplifier | 8.94–10.28 GHz | +7 dBm |
| ADF4351 IF | 0.56–1.23 GHz | 0 dBm |
| Mixer RF out | 9.5–11.5 GHz | –8 dBm |
| RF amplifier | 9.5–11.5 GHz | +7 dBm |
| Output pad / EQ | 9.5–11.5 GHz | 0 dBm |

---

## Control architecture

### Inputs to MCU (Raspi PICO)

- \( V_{\text{sweep}} \) (ADC)  
- 10 MHz reference  
- Band select

### MCU responsibilities

- Compute \( RF_{SA} = 2 \cdot V_{\text{sweep}} \)  
- Compute \( IF_{TG} = \dfrac{2IF_1 - RF_{SA}}{3} \)  
- Program ADF4351 via SPI  
- Manage LO enable/mute  
- Provide calibration hooks

### SPI layout

- Shared SPI bus  
- Dedicated CS for ADF4351  
- PLL Lock‑detect GPIO

---

## Calibration notes

- Amplitude flatness over 2 GHz span corrected via output pad/EQ.  
- \( LO_2 \) level set by fixed‑gain amplifier.  
- ADF4351 spur performance acceptable due to low IF offset.  
- Mixer conversion loss measured per module and stored in calibration table.

---

## Mechanical / layout notes

- Doubler → LO amp → HMC220 LO path are short and shielded.  
- Maintain isolation between \( LO_2 \) and RF output.  
- ADF4351 IF routing needs a clean 50 ohm but is not microwave‑critical.  
- Fine tune attenuator pads for correct levels at \( LO_2 \), \( IF_{TG} \), and \( RF_{TG} \).  
