# Hardware — TG Project

This directory contains all hardware designs for the modular Tracking Generator (TG).  
Each RF module is fully self‑contained with its own schematic, PCB layout, BOM, and test notes.

The hardware is organized into four primary modules:

- **Baseband Module (0–2.9 GHz)**
- **6 cm Module (4.3–7.2 GHz)**
- **3 cm Module (8.8–11.7 GHz)**
- **LO Board (central PLLs + 10 MHz reference)**

All PCBs are designed in KiCad and stored in separate subdirectories.

---

## Directory Structure

Each module directory contains:

- **`schematic.pdf`** — complete schematic  
- **`pcb/`** — KiCad PCB files, 3D views, fabrication output  
- **`bom.csv`** — Bill of Materials  
- **`readme.md`** — module‑specific documentation (gain budget, design notes, test plan)

---

## Hardware Module Overview

### **Baseband Module (0–2.9 GHz)**
- IF chain from the HMC219 downconverter  
- 3 GHz low‑pass filter  
- Pads, IF amplifier, SMA output  
- Purpose: provide a clean IF signal for the 6 cm and 3 cm upconverter modules

### **6 cm Module (4.3–7.2 GHz)**
- HMC220 mixer  
- IF and RF amplification  
- Band‑pass filtering for 4–8 GHz  
- Purpose: upconversion for the 6 cm tracking generator band

### **3 cm Module (8.8–11.7 GHz)**
- HMC220 mixer  
- High‑frequency RF amplifier (9–12 GHz)  
- Band‑pass filtering for 8–12 GHz  
- Purpose: upconversion for the 3 cm tracking generator band

### **LO Board**
- Three PLLs:
  - 3.9107 GHz (baseband LO)  
  - 4.3 GHz (6 cm LO)  
  - 8.8 GHz (3 cm LO)  
- LO amplifiers + SMA band‑pass filters  
- 10 MHz reference input  
- SPI control (Raspberry Pi or microcontroller)

---

## Design Principles

- **Modular:** each RF module is electrically and mechanically independent  
- **SMA‑based:** all interconnects use SMA cables for clean, predictable RF paths  
- **Reproducible:** BOMs, schematics, and PCB files are fully documented  
- **RF‑optimized:** short signal paths, solid grounding, controlled impedance  
- **Measurable:** each module includes clear test points and a documented gain budget

---

## Gain Budgets

Gain budgets for all modules are located in:

- **`docs/rf-design/gain-budgets/`**


