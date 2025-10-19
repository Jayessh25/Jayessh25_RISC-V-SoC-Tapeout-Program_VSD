# 🚀 Week 4 Day 1: BabySoC Fundamentals & Functional Modelling
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Day-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 4** **Day 1** of the **NgspiceSky130 learning series**!  
This day begins your journey into **transistor-level design and SPICE simulation** using the open-source **Sky130 PDK**.  
We’ll explore the electrical behavior of the **NMOS transistor** and understand how **drain current (Id)** varies with **drain-to-source voltage (Vds)** under different gate bias conditions. Day 1 — NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---
## 📑 Table of Contents

- [1. What is a System-on-Chip (SoC)?](#1-what-is-a-system-on-chip-soc)
- [2. Types of SoCs](#2-types-of-socs)
- [3. Components of a Typical SoC](#3-components-of-a-typical-soc)
- [4. Introduction to VSDBabySoC](#4-introduction-to-vsdbabysoc)
- [5. What is VSDBabySoC?](#5-what-is-vsdbabysoc)
- [6. Problem Statement](#6-problem-statement)
- [7. What is SoC?](#7-what-is-soc)
- [8. What is RVMYTH?](#8-what-is-rvmyth)
- [9. What is PLL?](#9-what-is-pll)
- [10. What is DAC?](#10-what-is-dac)
- [11. Project Structure](#11-project-structure)
- [12. TLV to Verilog Conversion for RVMYTH](#12-tlv-to-verilog-conversion-for-rvmyth)
- [13. Simulation Steps](#13-simulation-steps)
  - [13.1 Pre-Synthesis Simulation](#131-pre-synthesis-simulation)
  - [13.2 Viewing Waveform in GTKWave](#132-viewing-waveform-in-gtkwave)
  - [13.3 Viewing DAC Output in Analog Mode](#133-viewing-dac-output-in-analog-mode)
  - [13.4 Trouble Shooting Tips](#134-trouble-shooting-tips)
- [14. Why Pre-Synthesis and Post-Synthesis?](#14-why-pre-synthesis-and-post-synthesis)
- [15. Summary](#15-summary)

---

## 🎯 Learning Objectives

By the end of this session, you will:

- Understand why SPICE simulations are essential in circuit design.  
- Explore NMOS transistor fundamentals and body effect.  
- Analyze **linear (resistive)** and **saturation** regions of NMOS operation.  
- Simulate and plot **Id–Vds characteristics** using Sky130 SPICE models.

---

## 🧩 1. Introduction to Circuit Design and SPICE Simulations  

SPICE (Simulation Program with Integrated Circuit Emphasis) is a powerful tool for **predicting circuit performance** before fabrication.  
It allows us to simulate device behavior, verify electrical parameters, and visualize circuit responses to different inputs.

Key Topics:
- Purpose and advantages of SPICE simulations  
- Basics of NMOS structure and operation  
- Threshold voltage and strong inversion  
- Substrate (body) bias and threshold variation  

---

### 📘 Why SPICE?

In circuit design, SPICE provides a **virtual lab environment** that replicates real-world device behavior.  
For instance, consider a CMOS **inverter**:

- The **PMOS** (pull-up) and **NMOS** (pull-down) share a common output node.  
- Their **gates** receive the same input, ensuring complementary operation.  
- The **output** switches between **VDD** and **GND**, producing logical inversion.

SPICE allows designers to confirm correct switching behavior, analyze **propagation delays**, and study **power consumption** before committing to silicon fabrication.

---

## ⚙️ 2. NMOS Device Fundamentals  

### 🧠 Understanding the NMOS Transistor

An **NMOS transistor** conducts when a **positive gate voltage (Vgs)** exceeds the **threshold voltage (Vt)**.  
This voltage induces a conductive channel between **drain** and **source**, allowing current flow.

![NMOS Structure]()

**Key Parameters:**
- **Vt (Threshold Voltage):** Minimum gate voltage required to form an inversion channel.  
- **Strong Inversion:** Region where the induced channel is fully formed and current flow maximizes.  
- **Body Effect:** Variation of threshold voltage due to substrate potential (Vsb).  

---

### ⚡ Threshold Voltage and Body Effect

The threshold voltage is not constant — it changes with substrate bias (Vsb).  
SPICE models use parameters such as **body-effect coefficient (γ)** and **Fermi potential (ΦF)** to calculate this variation.

| Parameter | Description |
|------------|-------------|
| Vt0 | Threshold voltage when Vsb = 0 |
| γ (Gamma) | Body effect coefficient |
| ΦF | Fermi potential (process-dependent) |

These are foundry-defined parameters embedded in the **Sky130 model files** to ensure simulation accuracy.

![Threshold Variation]()

---

## 📊 3. NMOS Operation Regions  

The NMOS transistor operates in two major regions based on **Vds** and **Vgs**:

---

### 🔹 (a) Resistive / Linear Region

Occurs when **Vds < (Vgs - Vt)**.  
Here, the channel is continuous, and the transistor behaves like a **voltage-controlled resistor**.

![Linear Region]()

**Current Equation:**
\[
I_d = μ_n C_{ox} \frac{W}{L} [(V_{gs} - V_t)V_{ds} - \frac{V_{ds}^2}{2}]
\]

Where:
- **μn:** Electron mobility  
- **Cox:** Oxide capacitance per unit area  
- **W/L:** Width-to-length ratio of the MOSFET  

![Drift-Diffusion Model]()

The **drain current (Id)** increases linearly with **Vds**, forming the initial slope in the Id–Vds plot.

---

**SPICE Observation (Resistive Mode):**  
In simulation, for each **Vgs** value, **Vds** is swept, and Id is plotted.  
As **Vds** approaches **Vgs - Vt**, the linear behavior transitions into saturation.

![Resistive SPICE Curve]()

---

### 🔹 (b) Saturation / Pinch-off Region

Occurs when **Vds ≥ (Vgs - Vt)**.  
The channel pinches off near the drain, and current becomes almost constant.

![Saturation Concept]()

**Current Equation:**
\[
I_d = \frac{1}{2} μ_n C_{ox} \frac{W}{L} (V_{gs} - V_t)^2
\]

Here, **Id** is primarily controlled by **Vgs**, making this region essential for analog amplifiers and current sources.

![Saturation Graphs]()

**SPICE Observation (Saturation Mode):**
- Id saturates with increasing Vds.  
- The saturation level rises with higher Vgs values.  
- The transition point (**Vds = Vgs - Vt**) is clearly visible in the simulated plots.

![SPICE Saturation Curves]()

---

## 🧪 4. Introduction to SPICE Simulation Setup  

SPICE simulation bridges **theory** and **practical device behavior**.  
A typical SPICE file contains:

1. **Circuit Elements:** Transistors, voltage sources, and resistors.  
2. **Device Parameters:** Model files (Sky130 NMOS/PMOS) and geometry (W/L).  
3. **Analysis Commands:** `.dc`, `.tran`, `.ac`, etc. for different simulations.  

Example (Conceptual):


## 🧩 SPICE Netlist Example or Spice setup

Below is an example SPICE netlist used to simulate NMOS characteristics.

```spice
* Model Description
.param temp=27

* Include Sky130 Library
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

* Netlist Description
XM1 vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V

* Simulation Commands
.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control
run
display
setplot dc1
.endc
.end
```

## 🔍 Explanation

**Circuit Description:**

M1 vdd n1 0 0 nmos W=1.8u L=1.2u
R1 in n1 55
Vdd vdd 0 2.5
Vin in 0 2.5


- **M1 vdd n1 0 0 nmos W=1.8u L=1.2u** — Defines an **NMOS transistor** with the given **width** and **length**.  
- **R1 in n1 55** — Represents a **55Ω resistor** between nodes `in` and `n1`.  
- **Vdd vdd 0 2.5** — Provides a **2.5V supply voltage**.  
- **Vin in 0 2.5** — Acts as the **input voltage source**.  

This circuit defines all the essential components, parameters, and node connections required for a **SPICE simulation**.
![Screenshot]()
![Screenshot]()
![Screenshot]()
![Screenshot]()

## 🧾 Summary

| Concept | Description |
|----------|--------------|
| **Resistive Region** | Transistor acts as a resistor; Id ∝ Vds |
| **Saturation Region** | Id becomes constant; controlled by Vgs |
| **Threshold Voltage** | Minimum gate voltage for conduction |
---

## 🧪 LABS

### 1️⃣ Introduction to SPICE
**SPICE** (Simulation Program with Integrated Circuit Emphasis) is a powerful software tool used to **simulate and validate circuit performance**.  
It allows designers to analyze **voltage**, **current**, and **power behavior** before the actual hardware is fabricated.

---

### 2️⃣ SPICE with Sky130 Models

To run SPICE simulations using the **Sky130 PDK**, clone the official workshop repository:

```bash
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
```


**3 Important Files in the Repo**:

1. **`/sky130CircuitDesignWorkshop/design/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.pm3.spice`**  
   This file contains the SPICE model for the **NFET (N-channel MOSFET)** in the Sky130 process at typical (tt) conditions.

2. **`/sky130CircuitDesignWorkshop/design/sky130_fd_pr/cells/nfet_01v8/sky130_fd_pr__nfet_01v8__tt.corner.spice`**  
   This file provides the corner model for the **NFET**, used for simulating different process variations.

3. **`/sky130CircuitDesignWorkshop/design/sky130_fd_pr/models/sky130.lib.pm3.spice`**  
   This library file contains all the **SPICE models** for components in the Sky130 process node.

You can download ngspice for Windows from the official source using the following link:

[ngspice Downloads - SourceForge]()

### To plot the waveforms in ngspice
    ngspice day1_nfet_idvds_L2_W5.spice
    plot -vdd#branch

## 📈 NMOS Id–Vds Simulation Output

The following plots are obtained from SPICE simulations of an NMOS transistor using **Sky130 models**. They show how the **drain current (Id)** varies with **drain-to-source voltage (Vds)** for different gate voltages (Vgs).

---

### **1️⃣ Id vs Vds for Multiple Gate Voltages**

![Screenshot]()

**Description:**
- Shows **Id–Vds characteristics** for several Vgs values.  
- At low Vds, Id increases linearly — the **resistive (linear) region**.  
- At higher Vds, Id flattens — the **saturation region**.  
- Higher Vgs produces higher Id, consistent with NMOS behavior.  
- The transition from linear to saturation occurs approximately at **Vds = Vgs - Vt**.

**Observation:**  
This plot verifies the expected NMOS behavior and clearly distinguishes the linear and saturation regions.

---

### **2️⃣ Zoomed View of Linear Region**

![Screenshot]()

**Description:**
- Focused on the **low Vds region** of the NMOS.  
- Illustrates that Id **increases almost linearly with Vds**.  
- Confirms that in the linear region, the transistor behaves like a **voltage-controlled resistor**.  
- The slope of each curve increases with higher Vgs, showing the effect of gate voltage on channel conductance.

**Observation:**  
The linear region is clearly visible, providing insight into how the device will behave in analog circuits or as a switch with small Vds.

---

### **3️⃣ Id vs Vds in Saturation Region**

![Screenshot]()

**Description:**
- Focused on the **high Vds region**, where Id becomes nearly constant.  
- Illustrates the **saturation region (pinch-off)** of the NMOS transistor.  
- Id depends mostly on Vgs and shows minimal variation with further increase in Vds.  
- Confirms the SPICE simulation results match theoretical predictions for the saturation current.

**Observation:**  
This plot helps understand current limiting in NMOS transistors, critical for analog design and current sources.

---

**Summary of Observations:**
- **Linear region:** Id ∝ Vds, slope increases with Vgs.  
- **Saturation region:** Id saturates, controlled mainly by Vgs.  
- **Transition:** Occurs near Vds = Vgs - Vt.  
- SPICE results align well with theoretical NMOS behavior.

## 📈 Outcome  

By completing **Day 1**, you’ll gain a solid understanding of how NMOS transistors behave under various voltage conditions and how SPICE simulations bring theory to life.  
You’ll learn how to interpret transistor characteristics, analyze current-voltage behavior, and generate plots that reveal real design insights.  

This forms the **foundation of all transistor-based design work** — the stepping stone to CMOS logic and full-system chip verification.

✨ *Every silicon chip begins at the transistor level — master this, and you master the heart of digital design.* ✨
