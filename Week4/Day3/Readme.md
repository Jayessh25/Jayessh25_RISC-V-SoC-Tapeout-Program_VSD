# 🚀 Week 4 Day 3:  Transient analysis  & Voltage Transfer Chracteristics (VTC) For a CMOS Inverter
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-4-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 4** **Day 3** of the **NgspiceSky130 learning series**!  
This day begins your journey into Analyze the behavior of MOSFETs at Exploring CMOS inverter static and dynamic characteristics. Performing transient and DC simulations using NGSPICE and Sky130 PDK.

---
## 📑 Table of Contents

1. [Objective](#-objective-from-content)  
2. [Voltage Transfer Characteristics: SPICE Simulations](#1-voltage-transfer-characteristics-spice-simulations)  
3. [Static Behavior Evaluation: CMOS Inverter Robustness and Switching Threshold (Vm)](#static-behavior-evaluation-cmos-inverter-robustness-and-switching-threshold-vm)  
4. [Velocity Saturation and Switching Threshold (Vm) Analysis](#velocity-saturation-and-switching-threshold-vm-analysis)  
5. [Applications of CMOS Inverter in Clock Network and STA](#applications-of-cmos-inverter-in-clock-network-and-sta)  
6. [LABS](#labs)  
   - [Simulation 1 - Transient Analysis](#simulation-1---transient-analysis)  
   - [Simulation 2 - Voltage Transfer Characteristics (VTC)](#simulation-2---voltage-transfer-chracteristics-vtc)  
7. [Summary: Day 3 – CMOS Inverter Analysis](#summary-day-3--cmos-inverter-analysis)  
8. [Key Observations and Learning Points](#key-observations-and-learning-points)  
9. [Overall Outcome for Day 3](#overall-outcome-for-day-3)  
10. [Repository Info](#repository-info)
---
# 🎯 Objective (from content)

To analyze MOSFET behavior at lower technology nodes using SPICE simulations, focusing on:
- Exploring CMOS inverter static and dynamic characteristics.
- Performing transient and DC simulations using NGSPICE and Sky130 PDK.
- Understanding switching threshold (Vm) and propagation delay in CMOS logic.

---
  
# 1. Voltage Transfer Characteristics: SPICE Simulations

To explore the behavior of a **CMOS inverter** in SPICE, careful attention to circuit setup and parameters is essential. Think of it as **building a miniature digital world**, where every connection and value matters:

- **Connecting Components:** Ensure that **PMOS, NMOS, Vdd, Vss, Vin, and Vout** are correctly wired, so the inverter performs its switching magic.  
- **Defining Component Values:** Set the stage with accurate parameters — include **transistor sizes, threshold voltages (Vth), and supply voltage (Vdd)** — to make the simulation reflect real-world behavior.  
- **Identifying Nodes:** Map out every electrical node, such as **input (Vin), output (Vout), drain, source, and bulk terminals**, so you know exactly where the action is happening.  
- **Naming Nodes:** Give every node a clear, descriptive name (like **Vout** for the output) to make analysis intuitive and tracking results effortless.  

> By thoughtfully connecting and naming every piece of the circuit, SPICE becomes a powerful microscope to visualize how the inverter switches between logic states.

![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20162407.png)
![SCREESHOT](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20162421.png)
### SPICE simulation for CMOS inverter
SPICE Netlist for CMOS Inverter
A **SPICE netlist** for a CMOS inverter includes transistor models, power supply connections, input voltage source, transistor specifications (PMOS and NMOS), and simulation commands (e.g., `.tran` for transient analysis).
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20162435.png)
Same Wn/Ln = Wp/Lp = 1.5. Plot out vs in:
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20162457.png)
Now, Wn/Ln = 1.5 and Wp/Lp = 3.75. Plot out vs in:
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20163539.png)
![screen](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20163603.png)
## **Static Behavior Evaluation: CMOS Inverter Robustness and Switching Threshold (Vm)**  
The **switching threshold (Vm)** is the point where **Vin = Vout**, and both transistors are in saturation (since **Vds = Vgs**). At **Vm**, maximum power is drawn due to large current, and it can be graphically found at the intersection of the **VTC** with the **Vin = Vout** line. The analytical expression for **Vm** is obtained by equating the drain currents of PMOS and NMOS (**IDSn = IDSp**).
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20163652.png)


In the **velocity-saturated** case, the **switching threshold (Vm)** is the point where both **NMOS** and **PMOS** transistors are in saturation, and the drain currents are equal. This occurs when the **VDS** of both devices is less than the saturation voltage, i.e., **VDSAT < (Vm − VT)**. The threshold voltage **Vm** can be derived by equating the drain currents of both transistors, with the device widths and lengths (W/L ratios) playing a key role in determining the point where both transistors conduct equally.
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20171857.png)
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20171913.png)
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20171934.png)
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20171947.png
)
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172004.png)
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172016.png)


---

## Velocity Saturation and Switching Threshold (Vm) Analysis

- **Case 1: Velocity Saturation Occurs**  
  - Happens in short-channel devices or high supply voltage.  
  - Switching threshold **Vm** occurs when currents of PMOS and NMOS transistors are equal.  
  - The ratio **r** (PMOS to NMOS strength) influences **Vm**.  
  - For **Vm ≈ VDD/2**, **r ≈ 1**.  
  - **Vm** can be adjusted by changing the PMOS or NMOS width:  
    - Increase PMOS width → shift **Vm** upwards.  
    - Increase NMOS width → shift **Vm** downwards.  

- **Case 2: Velocity Saturation Does Not Occur**  
  - Applies to long-channel devices or low supply voltage.  
  - The switching threshold **Vm** is still affected by **r** but with a simpler formula.  
  - If **r ≈ 1**, **Vm** is near **VDD/2**.

- **PMOS Width Effect on VTC**  
  - Increasing PMOS width shifts **Vm** upwards.  
  - Increasing NMOS width shifts **Vm** downwards.  
  - **Vm** is relatively stable with small variations in transistor ratios.

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172031.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172114.png)

### Applications of CMOS inverter in clock network and STA
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172144.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/Screenshot%202025-10-19%20172129.png)

---

# LABS
## Simulation 1 - Transient analysis

## 🎯 Mission: Capture the Switching Event

**The Challenge**: Stop analyzing DC behavior and start measuring **real-time switching**. How fast can this inverter actually flip states?

**What We're After**:
- ⏱️ **Rise Time (tr)**: How long to go LOW → HIGH
- ⏱️ **Fall Time (tf)**: How long to go HIGH → LOW  
- ⚡ **Propagation Delays**: When does the output respond to input changes?
- 📊 **Overshoot/Undershoot**: Any transient glitches?

### 💡 Why Timing Matters

```
Faster Rise/Fall Time → Higher Maximum Clock Frequency
                     → Better Performance
                     → But also more dynamic power!
```

---

## 🧪 The Experiment Setup

### 🎛️ Input Stimulus: The PULSE Source

We're hitting the inverter with a **square wave** to simulate real logic transitions:

```spice
Vin in 0 PULSE(0 1.8 0 1n 1n 10n 20n)
                │  │  │  │  │  │   └─ Period (20ns → 50 MHz)
                │  │  │  │  │  └───── Pulse width (10ns)
                │  │  │  │  └──────── Fall time (1ns)
                │  │  │  └─────────── Rise time (1ns)
                │  │  └────────────── Delay (start immediately)
                │  └───────────────── High level (VDD)
                └──────────────────── Low level (0V)
```


### Step 1: Navigate to the Design Directory

We start by moving into the design directory where the Day 3 circuit files are located:

```
cd sky130CircuitDesignWorkshop/design/
```

This ensures we are in the correct folder for running the SPICE simulations.


### Step 2: Transient Analysis of a CMOS Inverter

We analyze the time-domain response of a CMOS inverter using the following SPICE file:

```
day3_inv_tran_Wp084_Wn036.spice
```

### Info about the File Being Simulated

This SPICE file defines a CMOS inverter and performs a transient analysis to simulate its switching response when the input voltage changes over time.

**Transistor parameters:**

- *PMOS Width (Wp)* = 0.84 µm

- *NMOS Width (Wn)* = 0.36 µm

**Purpose:** To study the inverter’s rise time, fall time, and propagation delay.

**Simulation Type:** Transient analysis (time-domain).


### Step 3: View the File Contents

We inspect the SPICE netlist using:

```
vim day3_inv_tran_Wp084_Wn036.spice
```

![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/ngspicetrans.png)


Open the netlist in vim so we can review transistor connections, input waveform, and transient analysis commands.


### Step 4: Run the Transient Simulation

We execute the simulation in NGSPICE:

```
ngspice day3_inv_tran_Wp084_Wn036.spice
plot out vs time in
```
![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/ngspicetrans%20plotout%20vs%20time%20ingraph.png)

![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/ngspicetrans%20plotout%20vs%20time%20ingraph%20values%20rise%20and%20fall%20delay.png)

This generates the input and output waveforms of the CMOS inverter, allowing us to measure rise/fall times and propagation delay.

### 🎯 Measured Timing Parameters

<table>
<tr>
<th>Parameter</th>
<th>Symbol</th>
<th>Measured Value</th>
<th>Measurement Points</th>
<th>Quality</th>
</tr>

<tr>
<td><strong>🔺 Rise Time</strong></td>
<td>tr</td>
<td><strong>661.76 ps</strong></td>
<td>10% → 90% (0.18V → 1.62V)</td>
<td>🟢 Fast</td>
</tr>

<tr>
<td><strong>🔻 Fall Time</strong></td>
<td>tf</td>
<td><strong>475.07 ps</strong></td>
<td>90% → 10% (1.62V → 0.18V)</td>
<td>🟢 Faster!</td>
</tr>

<tr>
<td><strong>⏱️ Prop. Delay (LH)</strong></td>
<td>tPLH</td>
<td>~662 ps</td>
<td>Vin=50% → Vout=50%</td>
<td>🟡 Moderate</td>
</tr>

<tr>
<td><strong>⏱️ Prop. Delay (HL)</strong></td>
<td>tPHL</td>
<td>~475 ps</td>
<td>Vin=50% → Vout=50%</td>
<td>🟢 Better</td>
</tr>

<tr>
<td><strong>🔝 Peak High</strong></td>
<td>VOH(peak)</td>
<td>1.809 V</td>
<td>Slight overshoot</td>
<td>✅ Normal</td>
</tr>

<tr>
<td><strong>🔻 Peak Low</strong></td>
<td>VOL(peak)</td>
<td>−4.94 mV</td>
<td>Minimal undershoot</td>
<td>✅ Excellent</td>
</tr>
</table>

## 🧠 Performance Insights

### 📊 Design Trade-offs Table

| Want Better... | Action Required | Side Effect |
|:---------------|:----------------|:------------|
| 🚀 **Faster Rise** | Increase PMOS width (Wp) | + Area, + Input cap, + Power |
| 🚀 **Faster Fall** | Increase NMOS width (Wn) | + Area, + Input cap, + Power |
| ⚖️ **Balanced tr/tf** | Adjust Wp/Wn ratio | Changes Vm position |
| 💚 **Lower Power** | Reduce widths | − Speed (slower edges) |
| 🎯 **Less Overshoot** | Add series resistance | − Speed, + Area |



## Simulation 2 - Voltage Transfer Chracteristics (VTC)

## 🏗️ Our Inverter Architecture

### 📐 Design Specifications

<table>
<tr>
<th>Component</th>
<th>Parameter</th>
<th>Value</th>
<th>Design Rationale</th>
</tr>

<tr>
<td rowspan="2"><strong>🔵 PMOS (M1)</strong></td>
<td>Width (W)</td>
<td>0.84 µm</td>
<td rowspan="2">2.33× wider than NMOS to compensate for lower hole mobility</td>
</tr>
<tr>
<td>Length (L)</td>
<td>0.15 µm</td>
</tr>

<tr>
<td rowspan="2"><strong>🟢 NMOS (M2)</strong></td>
<td>Width (W)</td>
<td>0.36 µm</td>
<td rowspan="2">Minimum size for acceptable drive strength</td>
</tr>
<tr>
<td>Length (L)</td>
<td>0.15 µm</td>
</tr>

<tr>
<td><strong>⚡ Load Cap</strong></td>
<td>Cload</td>
<td>50 fF</td>
<td>Typical fanout capacitance</td>
</tr>

<tr>
<td><strong>🔌 Supply</strong></td>
<td>VDD</td>
<td>1.8 V</td>
<td>Standard Sky130 nominal voltage</td>
</tr>
</table>

### 🧮 The Sizing Sweet Spot

**Why Wp/Wn ≈ 2.33?**

```
Goal: Balanced Vm ≈ VDD/2

Challenge: μn ≈ 2.5 × μp (electrons faster than holes!)

Solution: Make PMOS wider
         Wp/Wn ≈ μn/μp ≈ 2.5

Reality: We use 0.84/0.36 = 2.33 (close enough!)
```

---

### Step 1: Voltage Transfer Characteristics (VTC) of a CMOS Inverter

Next, we analyze the static behavior of the same inverter using:

```
day3_inv_vtc_Wp084_Wn036.spice
```

### Info about the File Being Simulated

This SPICE file performs a DC sweep of the inverter’s input voltage to generate the Voltage Transfer Characteristics (VTC).

**Transistor parameters:**

- *PMOS Width (Wp)* = 0.84 µm

- *NMOS Width (Wn)* = 0.36 µm

**Purpose:** To determine logic thresholds, switching point, and noise margins of the inverter.

**Simulation Type:** DC sweep of Vin.


### Step 2: View the File Contents

We check the netlist with:

```
vim day3_inv_vtc_Wp084_Wn036.spice
```

![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/ngspicevtc.png)


Allowing us to see the DC sweep setup, transistor sizing, and output node definition.


### Step 3: Run the VTC Simulation

We run the simulation in NGSPICE:

```
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs in
```


![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day3/Photo/ngspicevtc%20plotout%20vs%20time%20ingraph.png)

This produces the Vout vs Vin curve, from which we can determine the inverter’s switching threshold and noise margins.

### 🎯 Measured Parameters

| Parameter | Symbol | Measured Value | Expected Range | Status |
|:----------|:------:|:--------------:|:--------------:|:------:|
| **Switching Threshold** | Vm | **0.878723 V** | 0.8 - 1.0 V | ✅ Excellent |
| **Output High** | VOH | 1.800 V | ≈ VDD | ✅ Perfect |
| **Output Low** | VOL | ~0 V | < 50 mV | ✅ Perfect |
| **Supply Voltage** | VDD | 1.800 V | 1.8 V nominal | ✅ Nominal |

---

## Summary: Day 3 – CMOS Inverter Analysis

### Objective:

- To study both the dynamic and static behavior of a CMOS inverter using Sky130 NMOS and PMOS devices.

- To measure transient response parameters such as rise time, fall time, and propagation delay, and to understand inverter switching thresholds and noise margins.

### Activities Performed:

**Simulation 1: Transient Analysis (day3_inv_tran_Wp084_Wn036.spice)**

- Navigated to the design directory.

- Opened the SPICE file in vim to inspect transistor sizing, input waveform, and transient analysis commands.

- Ran the simulation in NGSPICE and plotted input and output voltages.

**Simulation 2: Voltage Transfer Characteristics (VTC) (day3_inv_vtc_Wp084_Wn036.spice)**

- Opened the SPICE file in vim to review the DC sweep setup and inverter connections.

- Executed the simulation in NGSPICE to generate the Vout vs Vin curve.

### Key Observations and Learning Points:

- Observed the propagation delay, rise time, and fall time of the inverter from transient analysis.

- Determined the switching threshold voltage and noise margins from the VTC curve.

- Reinforced understanding of CMOS inverter operation, including how transistor sizing affects speed and logic levels.

- Gained experience in combining transient and DC analyses to fully characterize a digital gate.

### Overall Outcome for Day 3:

- Developed hands-on skills in simulating CMOS inverter behavior using Sky130 PDK.

- Learned to analyze both dynamic and static characteristics of a digital logic gate.

- Built a foundation for more complex CMOS circuits in future labs.
  
- **Propagation Delay:**  
  The time difference (measured at 50% of input-output transition) between the input signal change and the corresponding output signal switch in a logic gate (e.g., inverter).

  ---


<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
