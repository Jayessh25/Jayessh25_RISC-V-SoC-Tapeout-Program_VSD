# 🏗️ System-on-Chip (SoC) Design Flow  

This document explains the **SoC design flow**, starting from specifications and ending with a manufacturable GDSII layout. The process is divided into three main outputs: **O1, O2, and O3**.

---

## 📷 SoC Design Flow Diagram  

<p align="center">
  <img src="https://github.com/<your-username>/<your-repo>/blob/main/Images/soc_design_flow.png" alt="SoC Design Flow" width="800"/>
</p>

---

## 🔹 O1 – Chip Modelling  
- **Input:** Specifications written as a **C model**  
- **Purpose:** Defines high-level chip functionality  
- **Verification:** A **C testbench** is used for validation  
- **Output:** Functional chip model before hardware design  

---

## 🔹 O2 – RTL Architecture  
- **Input:** Verified C model  
- **Process:** Translated into **RTL (Verilog)** → a soft copy of hardware  
- **Components included:**  
  - Processor  
  - Peripherals / IPs  
- **Further steps:**  
  - Synthesis generates a **Gate-Level Netlist**  
  - Macros (from synthesized RTL) are added  
  - Functional **Analog IPs** integrated  
- **Output:** Synthesized RTL design ready for integration  

---

## 🔹 O3 – SoC Integration  
- **Process:**  
  - Integration of processor, peripherals, macros, and analog IPs  
  - Addition of GPIOs and interfaces  
  - **Floorplanning, placement, CTS, and routing** performed  
- **Considerations:**  
  - Macros and analog IPs are given as **hard macros (HM)**  
  - Processor may be **soft logic** or a **hard macro**  
- **Output:** Complete SoC layout prepared for physical verification  

---

## 🔹 Final Stage – RTL2GDS  
- **Conversion:** SoC integration is converted into **GDSII format**  
- **Verification:**  
  - **DRC (Design Rule Check)**  
  - **LVS (Layout vs. Schematic)**  
- **End Result:** A **manufacturable chip layout** ready for fabrication  

---

## ✅ Summary  
1. **O1 – Chip Modelling:** C model + C testbench  
2. **O2 – RTL Architecture:** RTL + synthesis + macros/IPs  
3. **O3 – SoC Integration:** Integration + floorplanning + routing  
4. **RTL2GDS:** GDSII + DRC/LVS checks → Final chip  

