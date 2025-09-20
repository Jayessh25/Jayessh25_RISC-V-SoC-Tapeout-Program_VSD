# 🚀 Week 0: VLSI System Design (VSD) Program Foundation & SoC Design Flow

<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Week](https://img.shields.io/badge/Week-0-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to my **VLSI System Design (VSD) Program** repository! This week focused on understanding the **System-on-Chip (SoC) design flow**, setting up the development environment, and installing essential open-source tools for synthesis, simulation, and design tasks. The goal was to create a reliable and efficient workspace for building a RISC-V SoC from high-level specification to manufacturable layout.

---

## 🏗️ System-on-Chip (SoC) Design Flow

This document explains the SoC design flow, starting from high-level specifications and ending with a manufacturable GDSII layout.  
The process is divided into three key stages (O1, O2, O3), followed by RTL2GDS for fabrication.

### 📷 SoC Design Flow Diagram

<p align="center">
  <img src="https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week0/SOC%20Design%20flow.png" width="800"/>
</p>

---

### 🔹 O1 – Chip Modelling

- **Input:** Specifications written as a C model  
- **Purpose:** Defines high-level chip functionality (golden reference)  
- **Verification:** A C testbench validates system behavior  
- **Output:** Functional chip model before RTL design  

---

### 🔹 O2 – RTL Architecture

- **Input:** Verified C model  
- **Process:** Translated into RTL (Verilog) → a synthesizable digital representation  

#### Components included:
- Processor Core  
- Peripherals / IP blocks  

#### Further steps:
- RTL → Logic Synthesis → Gate-Level Netlist (GLN)  
- Addition of synthesized Macros  
- Integration of Analog IPs (e.g., PLL, ADC/DAC)  

- **Output:** Synthesized RTL architecture ready for backend flow  

---

### 🔹 O3 – SoC Integration

- **Process:** Integration of core, peripherals, macros, and analog IPs  
- **Addition:** GPIOs, bus interconnects, and external interfaces  

#### Physical Design Steps:
- Floorplanning  
- Placement  
- Clock Tree Synthesis (CTS)  
- Routing  

#### Considerations:
- Hard Macros (e.g., SRAM, Analog IPs) are pre-designed blocks  
- Processor may be integrated as Soft Logic (synthesizable RTL) or Hard Macro (fixed layout)  

- **Output:** SoC layout prepared for physical verification  

---

### 🔹 Final Stage – RTL2GDS

- **Conversion:** Synthesized SoC → GDSII format (industry standard for fabrication)  

#### Verification:
- DRC (Design Rule Check) – ensures design follows foundry rules  
- LVS (Layout vs. Schematic) – checks schematic-to-layout consistency  
- Timing Closure & Power Analysis – signoff before tape-out  

- **End Result:** A manufacturable GDSII chip layout ready for fabrication  

---

### ✅ Summary

| Stage      | Output                              | Tools Involved                |
|------------|------------------------------------|-------------------------------|
| 🟦 O1      | Functional C Model                  | C/C++, SystemC               |
| 🟩 O2      | RTL + GLN                           | Verilog, Yosys/Synopsys DC   |
| 🟨 O3      | Integrated SoC Layout               | OpenROAD, Innovus             |
| 🟥 RTL2GDS | GDSII Layout                        | Magic, Calibre, OpenLane      |

**Stages Recap:**

1. **O1 – Chip Modelling:** C model + C testbench (golden reference)  
2. **O2 – RTL Architecture:** RTL + synthesis + macros/IPs  
3. **O3 – SoC Integration:** Integration + floorplanning + routing  
4. **RTL2GDS:** GDSII + DRC/LVS/timing → Final chip ready for tape-out  

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>
