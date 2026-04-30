# Tracking Generator for the HP 8562A/B Spectrum Analyzer (Work in Progress)
### Reconstructing the Analyzer’s RF Sweep from Its Local Oscillator

The HP 8562A/B spectrum analyzer does not include a tracking generator.  
Yet it exposes just enough of its internal architecture to make one possible if you understand how the analyzer really works.

This project implements a fully external tracking generator that behaves as if it were a native part of the HP 8562A/B system.  
It reconstructs the analyzer’s swept RF from the only two signals the analyzer provides:

- **1st LO Output** (3.9–6.8 GHz depending on band)  
- **LO Sweep Output** (0–10 V, 0.5 V/GHz)

The TG becomes a slave to the analyzer’s internal LO system and rebuilds the RF sweep in real time.

---

# 1. Why a Tracking Generator for the HP 8562A/B?

The HP 8562A/B is a legendary instrument: fast, sensitive, and extremely stable.  
But it lacks a built‑in tracking generator, which limits its usefulness for:

- filter alignment  
- cavity tuning  
- antenna measurements  
- scalar network analysis  
- frequency response characterization

This project fills that gap by creating a TG that follows the analyzer’s sweep exactly, across multiple microwave bands.

---

# 2. How the HP 8562A/B Actually Sweeps

Unlike modern VNAs or scalar analyzers, the HP 8562A/B does **not** sweep an RF source.  
Instead, it sweeps its **first local oscillator** and mixes incoming signals down to a fixed IF.

To cover 1 kHz–22 GHz, the analyzer uses **automatic harmonic mixing**:

| RF Band | Harmonic n | IF (GHz) |
|---------|------------|----------|
| 0–2.9 GHz | 1 | 3.9107 |
| 2.75–6.46 GHz | 2 | 3.107 |
| 5.86–13.0 GHz | 3 | 3.107 |
| 12.4–19.7 GHz | 4 | 3.107 |
| 19.1–22.0 GHz | 5 | 3.107 |

This means the analyzer LO follows: `n * LO_SA = RF + IF`

The LO is therefore **non‑linear** with respect to the displayed frequency.  
The TG must compensate for this non‑linearity to stay aligned with the analyzer.

---

# 3. Reconstructing the RF: The Core Idea

The analyzer exposes its 1st LO on a frontpanel connector.  
If the TG generates a second LO and mixes it with the analyzer LO, we can rebuild the RF: `RF_TG = LO_SA + LO_TG`

To make the TG output equal the analyzer’s displayed frequency: `RF_TG = RF_SA`

So the TG must compute: `LO_TG = RF_SA - LO_SA`

Whether the TG needs a sweeping LO depends on the harmonic mode of the band. In the baseband, the analyzer operates in fundamental mode (n = 1). The LO is linear, predictable, and easy to follow — the TG can simply use a fixed LO.

The higher bands are different. Once the analyzer switches to harmonic mixing (n > 1), the LO becomes non‑linear with respect to the displayed frequency. To keep the TG aligned with the analyzer, the TG must generate a second LO that sweeps in step with the analyzer’s LO.

Some HP 8562A/B bands can operate in more than one harmonic mode. To avoid ambiguity and ensure clean sweep behavior, the 6 cm TG module intentionally supports only one harmonic mode. This slightly narrows the usable frequency range, but greatly improves stability and linearity.

---

# 4. When LO_TG Must Sweep — and When It Does Not

This is the corrected explanation.

## Baseband (0–2.9 GHz) — LO_TG does *not* sweep
- Harmonic n = 1  
- LO_SA = RF_SA + IF  
- LO_SA is **perfectly linear**  
- The TG can use a **fixed LO**  
- No MAX2870 sweep required

This is why the BB module is simple.

## Microwave bands (6 cm, 3 cm, 15 mm) — LO_TG *must* sweep
In these bands, the analyzer uses harmonic mixing (n > 1): `LO_SA = (RF_SA + IF) / n `


This makes LO_SA **non‑linear** with respect to RF_SA.  
To reconstruct the RF sweep, the TG must generate: `LO_TG = RF_SA - LO_SA'

Since LO_SA changes during the sweep, **LO_TG must track the sweep as well**.

This is the fundamental reason the TG uses a MAX2870 synthesizer in the higher bands.

---

# 5. The Sweep Voltage: The Only Control Signal We Get

The HP 8562A/B provides a **0–10 V sweep output** at 0.5 V/GHz.  
This is the only real‑time indication of the analyzer’s tuned frequency.

The TG reads this voltage via an ADC: `RF_SA = 2 * V_sweep   (GHz)`

From this, the TG computes:
- the analyzer LO (LO_SA)  
- the required TG LO (LO_TG)  
- and programs the MAX2870 accordingly

This makes the TG a **true analog follower** of the analyzer.

---

# 6. Band‑Specific Constraints

Each frequency band has different constraints, driven by the analyzer’s harmonic mode and the required LO ranges. Each module is optimized for:

- LO ranges  
- mixer performance  
- filtering  
- gain control  
- sweep linearity  
- harmonic suppression

## Baseband (0–2.9 GHz)
- LO_SA is linear  
- TG uses a fixed LO  
- No MAX2870 sweep required

## 6 cm Band (~5.3–6.2 GHz)
- Harmonic 2  
- LO_SA is non‑linear  
- LO_TG must sweep 1.10–1.55 GHz  
- Mixers operate comfortably

## 3 cm Band (9–12 GHz)
- Harmonic 3  
- LO_SA is strongly non‑linear  
- LO_TG must sweep 4.96–6.96 GHz  
- Mixers operate near their upper linearity limit  
- Requires careful LO drive and filtering

## 15 mm Band (20–22 GHz internal)
- Harmonic 5  
- LO_SA is highly compressed  
- LO_TG must sweep 4.62–5.02 GHz  
- Mixer output is 20–22 GHz  
- Requires high‑frequency PCB or waveguide techniques

---

# 7. Firmware Logic: Computing the LO Frequencies

The TG uses the sweep voltage to compute the analyzer frequency: `RF_SA = 2 * V_sweep   (GHz)`

Then applies the correct harmonic‑mode formula:

| TG Band | Harmonic n | Analyzer LO (LO_SA) | TG LO (LO_TG) |
|---------|------------|----------------------|----------------|
| Baseband | 1 | LO_SA = RF_SA + 3.9107 | Fixed LO |
| 6 cm | 2 | LO_SA = (RF_SA + 3.107) / 2 | LO_TG = (RF_SA - 3.107) / 2 |
| 3 cm | 3 | LO_SA = (RF_SA + 3.107) / 3 | LO_TG = (2 * RF_SA - 3.107) / 3 |
| 15 mm | 5 | LO_SA = (RF_SA + 3.107) / 5 | LO_TG = (4 * RF_SA - 3.107) / 5 |

The MCU programs the MAX2870 with LO_TG on every sweep step.

---

# 8. Mixer Linearity and Sweep Fidelity

Because the TG output is the sum of two LOs, any non‑linearity in:

- the sweep voltage  
- the ADC  
- the MAX2870 tuning curve  
- the mixer conversion gain  
- LO drive levels  

will distort the RF sweep.

This is especially critical in the 3 cm and 15 mm bands.  
The TG therefore uses:

- buffered LO inputs  
- leveled LO drive  
- clean MAX2870 output filtering  
- stable SPI update timing  
- low‑noise analog front‑end for the sweep voltage

This ensures the TG sweep remains aligned with the analyzer across all bands.

---

# 9. Architecture Overview

```
HP 8562A/B
 │
 ├── LO Sweep Out (0–10 V) ──► ADC ──► MCU ──► MAX2870 (LO_TG)
 │
 └── 1st LO Out ──────────────► Mixer ◄────── LO_TG
                                  │
                                  ▼
                           TG RF Output
                           (RF_TG = RF_SA)
```

The TG is therefore a **frequency reconstruction engine**, not a simple swept oscillator.

---

## Repository structuer

- `docs/` — design documentation, gain budgets, LO chains, module descriptions
- `hardware/` — schematics, PCBs, BOMs
- `modules/` — datasheets and measurement data of ready‑made SMA modules
- `analysis/` — simulations, notebooks, calculations
- `mechanical/` — CNC enclosures, front panels, 3D models
- `scripts/` — measurement and calibration scripts

# 11. License

MIT License.  
See LICENSE file for details.

