# 🚀 Week 4 Day 2:  SPICE Simulation for Lower Nodes and Velocity Saturation Effect
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-4-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 4** **Day 2** of the **NgspiceSky130 learning series**!  
This day begins your journey into Analyze the behavior of MOSFETs at lower nodes using SPICE simulations.Study **velocity saturation effects** in short-channel devices.Understand **CMOS inverter characteristics** and **MOSFET as a switch**.  

---
## 📑 Table of Contents

1. [Objective](#objective)  
2. [SPICE Simulation for Lower Nodes](#spice-simulation-for-lower-nodes)  
3. [Ids vs Vgs Characteristics](#ids-vs-vgs-characteristics)  
4. [Velocity vs Electric Field Characteristics](#velocity-vs-electric-field-characteristics)  
5. [Ids vs Vds with Velocity Saturation](#ids-vs-vds-with-velocity-saturation)  
6. [CMOS Inverter Characteristics](#cmos-inverter-characteristics)  
7. [MOSFET as a Switch](#mosfet-as-a-switch)  
8. [Introduction to Standard MOS Voltage-Current Parameters](#introduction-to-standard-mos-voltage-current-parameters)  
9. [PMOS/NMOS Drain Current vs Drain Voltage](#pmosnmos-drain-current-vs-drain-voltage)  
10. [Steps for CMOS VTC Derivation](#steps-for-cmos-vtc-derivation)  
    - [Step 1: Convert PMOS Gate-Source Voltage](#step1-convert-pmos-gate-source-voltage-to-vin)  
    - [Step 2 & 3: Convert PMOS/NMOS Drain-Source Voltages](#step-2--step-3)  
    - [Step 4: Merge PMOS and NMOS Load Curves](#step-4-merge-the-pmos-and-nmos-load-curves)  
11. [LABS](#labs)  
    - [Example 1 – Ids vs Vds over Constant Vgs](#example-1--ids-vs-vds-over-constant-vgs)  
    - [Example 2 – Ids vs Vgs over Constant Vds](#example-2--ids-vs-vgs-over-constant-vds)

---

## Objective
- Analyze the behavior of MOSFETs at lower nodes using SPICE simulations.  
- Study **velocity saturation effects** in short-channel devices.  
- Understand **CMOS inverter characteristics** and **MOSFET as a switch**.  

---

## SPICE Simulation for Lower Nodes

- Simulations were carried out for **long-channel and short-channel MOSFETs**.  
- **Long-channel MOSFETs**: Drain current (**Ids**) shows **quadratic dependence** on gate voltage (**Vgs**).  
- **Short-channel MOSFETs**: Ids is **quadratic at low Vgs** and **linear at higher Vgs** due to **velocity saturation**.
- Shows how Ids varies with Vgs for both long-channel and short-channel devices.  
- **Long-channel MOSFETs**: Quadratic increase.  
- **Short-channel MOSFETs**: Linear Ids at higher Vgs due to **velocity saturation**.  

![Ids vs Vgs 1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20142946.png
)  
![Ids vs Vgs 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143128.png) 
![Ids vs Vgs 3](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143148.png
)  
![Ids vs Vgs 4](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143212.png
)  
![Ids vs Vgs 5](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143239.png
)  

**Observation:**  
- Short-channel devices show **early saturation** at higher Vgs.  
- Long-channel devices continue quadratic behavior across the range.
- Demonstrates **velocity behavior of carriers** with increasing electric field.  
- **Low electric field:** Linear velocity increase.  
- **High electric field:** Velocity **saturates**, leading to **velocity saturation effects** in short-channel devices.  

![Velocity vs Electric Field 1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143259.png)  
![Velocity vs Electric Field 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143317.png)  
![Velocity vs Electric Field 3](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143342.png)  

**Observation:**  
- High field region leads to **Ids saturation** even when Vgs increases.  
- Confirms **velocity saturation effect in short-channel MOSFETs**.
- Illustrates **Ids vs Vds** for different Vgs values considering **velocity saturation**.  
- Short-channel devices reach saturation **earlier** than long-channel devices due to **Vdsat effect**.  

![Vdsat Model 1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143401.png
)  
![Vdsat Model 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143425.png)  
![Vdsat Model 3](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143439.png)  

**Observation:**  
- Saturation occurs when **Vds ≥ Vdsat**.  
- Short-channel MOSFETs show **earlier flattening of Ids**, confirming velocity saturation.
- **Linear region (Vds < Vdsat):** Ids increases linearly with Vds.  
- **Saturation region (Vds ≥ Vdsat):** Ids becomes constant.  
- NMOS: Ids depends on **Vgs - Vth**.  
- PMOS: Ids depends on **Vth - Vgs**.  
- NMOS and PMOS devices show **expected linear and saturation regions**.  
- Confirms **non-linear resistor behavior** of MOSFETs.
- Shows the **switching behavior** of CMOS inverter.  
- **Vout is high** when Vin is low, and **Vout is low** when Vin is high.  
- Transition region defines **switching threshold** where both NMOS and PMOS conduct simultaneously.  

![CMOS VTC 1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143802.png)  
![CMOS VTC 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143816.png)  
![CMOS VTC 3](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143834.png)  
**Velocity Saturation Drain Current Model:**  
- **Technology Parameter:** Saturation voltage (**Vdsat**) – the voltage at which carrier velocity saturates, independent of **Vgs** or **Vds**.  
- **Vgt** is minimum → FET is in **saturation region** (both long and short channel).  
- **Vds** is minimum → FET is in **linear region** (both long and short channel).  
- **Vdsat** is minimum → FET velocity **saturates** (**only for short channel**).
 
## **CMOS Voltage Transfer Characteristics (VTC):**  
1. Vout is **high** when Vin is low, and Vout is **low** when Vin is high.  
2. The **transition region** shows a steep drop where both NMOS and PMOS conduct, defining the switching threshold.
#### MOSFET as a switch
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143847.png)
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143900.png)
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143924.png)
**Introduction to Standard MOS Voltage-Current Parameters:**  
In MOS devices, **Rp** (PMOS) and **Rn** (NMOS) act as **non-linear resistors**, where their resistance is a **function of drain current (Ids)**, influenced by gate voltage (Vgs) and drain voltage (Vds).
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20143936.png)
**PMOS/NMOS Drain Current vs. Drain Voltage:**

1. **Linear Region (Vds < Vdsat):**  
   - Drain current (**Ids**) increases **linearly** with **Vds**.  
   - Device behaves like a **resistor** controlled by Vgs.  

2. **Saturation Region (Vds ≥ Vdsat):**  
   - Drain current (**Ids**) becomes **constant** and independent of Vds.  
   - For **NMOS**, Ids depends on **Vgs - Vth**.  
   - For **PMOS**, Ids depends on **Vth - Vgs**.  
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20144002.png)
 #### Step1. Convert **PMOS gate-source voltage** to **Vin**.  
 Replace internal node voltages with **Vin**, **Vdd**, **Vss**, and **Vout**.  
 Simulate and plot the **VTC** by varying **Vin** and analyzing **Vout**.
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20144028.png)
**Step 2 & Step 3:**  
- Convert **PMOS** and **NMOS drain-source voltages** to **Vout**.  
- Relate the drain-source voltages of both devices to the output voltage (**Vout**) by considering their respective operating regions (linear or saturation).
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20144047.png)
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20144115.png)
**Step 4:**  
- **Merge the PMOS and NMOS load curves** by combining their drain current (Ids) characteristics with respect to **Vout**.  
- **Plot the Voltage Transfer Characteristic (VTC)** by mapping **Vout** against **Vin**, showing the transition from low to high output voltage.
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/Screenshot%202025-10-19%20144138.png)

---

## ⚡ Enter Velocity Saturation: The Plot Twist!

### 🏃 When Electrons Hit Their Speed Limit

In **short-channel devices** (L < 250 nm), something amazing happens: electrons reach a **maximum velocity** (~10⁷ cm/s in silicon) and refuse to go faster, no matter how hard you push!

<table>
<tr>
<th></th>
<th>🐌 Long Channel (L > 1 µm)</th>
<th>🚀 Short Channel (L < 250 nm)</th>
</tr>

<tr>
<td><strong>Operating Modes</strong></td>
<td>
• Cutoff<br/>
• Linear<br/>
• Saturation
</td>
<td>
• Cutoff<br/>
• Linear<br/>
• <strong>Velocity Saturation ⚡</strong><br/>
• Saturation
</td>
</tr>

<tr>
<td><strong>Current Depends On</strong></td>
<td>Id ∝ (Vgs − Vt)²</td>
<td>Id ∝ (Vgs − Vt) <em>← Linear!</em></td>
</tr>

<tr>
<td><strong>Max Current</strong></td>
<td>~410 µA</td>
<td>~210 µA <em>😢</em></td>
</tr>

<tr>
<td><strong>Switching Speed</strong></td>
<td>Moderate</td>
<td>⚡ <strong>Faster!</strong> ⚡</td>
</tr>

<tr>
<td><strong>Trade-off</strong></td>
<td>Higher current, slower</td>
<td>Lower current, but speed demon!</td>
</tr>
</table>

### 🧮 The Velocity Saturation Equation

When electrons max out their speed:

```
Id = W · Cox · vsat · (Vgs − Vt)
       ↑    ↑     ↑      ↑
       │    │     │      └─ Overdrive voltage
       │    │     └──────── Saturation velocity (~10⁷ cm/s)
       │    └────────────── Gate oxide capacitance
       └─────────────────── Transistor width
```

**The Key Insight**: Current becomes **linear** with (Vgs − Vt) instead of quadratic!

---

# LABS
## SPICE simulation for lower nodes and velocity saturation effect
#### Sky130 Id-Vgs
##### EXAMPLE 1
      *Model Description
      .param temp=27

      *Including sky130 library files
      .lib "sky130_fd_pr/models/sky130.lib.spice" tt

      *Netlist Description
       XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
       R1 n1 in 55
       Vdd vdd 0 1.8V
       Vin in 0 1.8V

      *simulation commands
       .op
       .dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

       .control

       run
       display
       setplot dc1
       .endc
       .end

**🚀 Launch Command**:
```bash
ngspice day2_nfet_idvds_L015_W039.spice
```

**📊 Visualization**:
```
plot -vdd#branch
```

#### The plot of Ids vs Vds over constant Vgs:
![Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/ngspice%20day2vdsnetlistcode.png
)
![Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/ngspice%20day2vdswaveform%2Bcommand.png)

##### EXAMPLE 2
      *Model Description
       .param temp=27

       *Including sky130 library files
       .lib "sky130_fd_pr/models/sky130.lib.spice" tt

       *Netlist Description
        XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15
        R1 n1 in 55
        Vdd vdd 0 1.8V
        Vin in 0 1.8V

        *simulation commands
         .op
        .dc Vin 0 1.8 0.1 

        .control

         run
         display
         setplot dc1
         .endc
         .end


**🚀 Launch Command**:
```bash
ngspice day2_nfet_idvgs_L015_W039.spice
```

**📊 Visualization**:
```
plot -vdd#branch
```

####  The plot of Ids vs Vgs over constant Vds:
![ Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/ngspice%20day2vgsnetlistcode.png
)
![ Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day2/Photo/ngspice%20day2vgswaveform%2Bcommand.png)

---

## 🎯 Threshold Voltage Extraction: The Detective Work

### 🔍 Method: Square Root Extrapolation

**The Technique**:

1. Plot **√Id vs Vgs** (instead of Id vs Vgs)
2. Find the **linear region** in strong inversion
3. Extrapolate back to x-axis
4. **X-intercept = Vt** ✨

```
√Id
 │     ╱
 │    ╱  ← Linear region (extrapolate this!)
 │   ╱
 │  ╱
 │ ╱
 │╱_______________
 └────────────────> Vgs
         Vt
         ↑
    Found it!
```

### 📊 Threshold Voltage vs. Channel Length

| Channel Length | Vt (approx) | Why Different? |
|:---------------|:------------|:---------------|
| **L = 2 µm** | ~0.45 V | Long channel—minimal SCE |
| **L = 0.5 µm** | ~0.42 V | Moderate SCE |
| **L = 0.15 µm** | ~0.38 V | Short-channel effects reduce Vt |

**SCE = Short-Channel Effects**: As L shrinks, source/drain depletion regions "help" the gate control the channel, slightly lowering Vt.

---

## 🎛️ The CMOS Inverter Connection

### 🔄 Digital Logic: MOSFETs as Switches

<div align="center">

| Input (Vin) | PMOS State | NMOS State | Output (Vout) |
|:-----------:|:----------:|:----------:|:-------------:|
| **0 V** | ✅ ON | ❌ OFF | **VDD** (logic 1) |
| **VDD** | ❌ OFF | ✅ ON | **0 V** (logic 0) |

</div>

**The Magic**: When one transistor is ON, the other is OFF—perfect complementary action!

<div align="center">

![MOSFET Switch Model](Images/Task2_11.png)

*Individual MOSFET as a switch—controlled by gate voltage*

</div>

<div align="center">

![CMOS Inverter](Images/Task2_12.png)

*Two switches working together = digital inverter!*

</div>

### 🎚️ MOSFET as a Voltage-Controlled Switch

| State | Condition | Resistance | Analogy |
|:------|:----------|:-----------|:--------|
| **OFF** | Vgs < Vt | ~∞ Ω | Open circuit (air gap) |
| **ON** | Vgs > Vt | ~kΩ | Closed switch (wire) |

---

## 📈 Load-Line Analysis: Finding the Switching Point

### 🔍 The Graphical Method

**The Challenge**: Where do PMOS and NMOS currents match? That's your switching threshold!

<table>
<tr><th>Step</th><th>What We Do</th><th>Visual Guide</th></tr>

<tr>
<td><strong>1️⃣</strong></td>
<td>Express PMOS gate voltage<br/><code>VgsP = Vin − VDD</code></td>
<td><img src="Images/Task2_14.png" alt="Step 1"/></td>
</tr>

<tr>
<td><strong>2️⃣</strong></td>
<td>Substitute internal nodes<br/>Replace with Vout everywhere</td>
<td><img src="Images/Task2_15.png" alt="Step 2"/></td>
</tr>

<tr>
<td><strong>3️⃣</strong></td>
<td>Find the crossover point<br/><code>IdN = IdP</code> → <strong>Vm</strong></td>
<td><img src="Images/Task2_16.png" alt="Step 3"/></td>
</tr>
</table>

**The Switching Point (Vm)**:
- Where NMOS and PMOS currents are equal
- Determines noise margins (NML, NMH)
- Critical for timing analysis!

---

## 🎓 Key Insights & Takeaways

### 💎 The Golden Nuggets

<table>
<tr>
<th>Discovery</th>
<th>Why It Matters</th>
<th>Design Impact</th>
</tr>

<tr>
<td><strong>🎚️ Threshold Voltage is Extractable</strong></td>
<td>Vt defines when transistor "turns on"</td>
<td>Sets minimum Vgs for reliable switching</td>
</tr>

<tr>
<td><strong>⚡ Velocity Saturation Limits Speed</strong></td>
<td>Electrons have a speed limit in silicon</td>
<td>Can't make transistors arbitrarily fast by shrinking L</td>
</tr>

<tr>
<td><strong>📐 Short Channels = Lower Current</strong></td>
<td>Vel. sat. reduces Id by ~50%</td>
<td>Must increase W to compensate</td>
</tr>

<tr>
<td><strong>🔗 Transistor Behavior ↔ Gate Delay</strong></td>
<td>Id determines how fast C charges</td>
<td>Higher Id = faster circuits (but more power!)</td>
</tr>
</table>

### 🎯 The Velocity Saturation Paradox

```
Shorter channel → Faster switching ✓
       ↓
But also: Higher E-field → Velocity saturation
       ↓
Result: Lower current ✗
       ↓
Solution: Increase W to compensate
       ↓
Trade-off: More area, more capacitance
```

---

## 🧠 Connecting to Circuit Design

### 🔗 From Transistor to Timing

```
MOSFET Id-Vgs curve
       ↓
Threshold voltage (Vt)
       ↓
Switching threshold (Vm) of inverter
       ↓
Noise margins (NML, NMH)
       ↓
Timing margins & setup/hold times
       ↓
Maximum clock frequency!
```

### ⚖️ The Designer's Dilemma

| Want More... | Must Accept... | Design Knob |
|:-------------|:---------------|:------------|
| 🚀 Speed | ⚡ Higher power | Increase W/L |
| 💚 Low power | 🐌 Slower gates | Decrease W, increase L |
| 🎯 Drive strength | 📏 Larger area | Increase W |
| 🛡️ Noise immunity | 📉 Smaller swing | Adjust Wp/Wn ratio |

---
---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
