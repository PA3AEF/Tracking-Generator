                           ┌──────────────────────────┐
                           │  Spectrum Analyzer (SA)  │
                           │  1st LO Out (3.9–6.8GHz) │
                           └─────────────┬────────────┘
                                         │
                                 Isolator 3–7GHz
                                         │
                                         ▼
                              ┌──────────────────┐
                              │   HMC219 Box     │
                              │  (Downconverter) │
                              └───────┬──────────┘
                                      │ IF Out 0–2.9GHz
                                      ▼
                         ┌────────────────────────────┐
                         │   Baseband Module (PCB)    │
                         │  LPF 3GHz + Pad + SMA Out  │
                         └───────────┬────────────────┘
                                     │
                                     ▼
                     ┌──────────────────────────────┐
                     │  6 cm Module (4.3–7.2 GHz)   │
                     │  HMC220 + IF/RF amps + BPF   │
                     └───────────┬──────────────────┘
                                 │
                                 ▼
                     ┌──────────────────────────────┐
                     │  3 cm Module (8.8–11.7 GHz)  │
                     │  HMC220 + IF/RF amps + BPF   │
                     └───────────┬──────────────────┘
                                 │
                                 ▼
                           TG Output SMA


LO‑Board (centrale PLL’s)
───────────────────────────────────────────────────────────────
  - PLL 3.9107 GHz → Amp → BPF 3.1–4.1 GHz → HMC219 LO
  - PLL 4.3 GHz    → Amp → BPF 4–8 GHz     → 6 cm Module LO
  - PLL 8.8 GHz    → Amp → BPF 8–12 GHz    → 3 cm Module LO
  - 10 MHz Ref In (SA)
  - Raspberry Pi control (SPI)
