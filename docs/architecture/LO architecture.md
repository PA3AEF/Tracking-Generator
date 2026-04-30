## Tracking Generator LO Architecture  
### Firmware formulas per band (TG follows SA via LO Sweep Output)

The HP 8562A/B provides an analog sweep voltage: `f_SA = 2 * V_sweep   (GHz)`

The Tracking Genegerator computes from this:

- the analyzer 1st LO: `f_LO_SA` using the correct harmonic mode
- TG internal LO (MAX2870): `f_LO_TG` 
- ensuring that the TG output always tracks the analyzer frequency: `f_RF_TG = f_SA`

---

## Firmware Table (LO formulas per band)

| TG Band | SA RF Range | Harmonic n | IF (GHz) | Analyzer LO (f_LO_SA) | TG LO (f_LO_TG) | TG LO Range (GHz) |
|---------|--------------|------------|----------|-------------------------|------------------|--------------------|
| Baseband (BB) | 0–2.9 GHz | 1 | 3.9107 | f_LO_SA = f_SA + 3.9107 | Fixed LO | n/a |
| 6 cm | 5.3–6.2 GHz | 2 | 3.107 | f_LO_SA = (f_SA + 3.107) / 2 | f_LO_TG = (f_SA - 3.107) / 2 | 1.10–1.55 |
| 3 cm | 9–12 GHz | 3 | 3.107 | f_LO_SA = (f_SA + 3.107) / 3 | f_LO_TG = (2 * f_SA - 3.107) / 3 | 4.96–6.96 |
| 15 mm | 20–22 GHz | 5 | 3.107 | f_LO_SA = (f_SA + 3.107) / 5 | f_LO_TG = (4 * f_SA - 3.107) / 5 | 4.62–5.02 |

---

## Sweep Architecture Summary

1. SA LO Sweep Output (0–10 V, 0.5 V/GHz) → attenuator → buffer → ADC  
2. MCU computes:  
   - `f_SA = 2 * V_sweep`  
   - `f_LO_SA` using harmonic mode  
   - `f_LO_TG` using formulas above  
3. MCU programs MAX2870 to generate `f_LO_TG`  
4. Mixer output:  
   - `f_RF_TG = f_LO_SA + f_LO_TG`
   
5. TG output tracks the analyzer frequency aacross all supported bands.

