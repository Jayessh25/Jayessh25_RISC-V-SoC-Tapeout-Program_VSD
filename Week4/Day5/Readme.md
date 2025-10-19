# 🚀 Week 4 Day 5:  CMOS Power Supply & Device Variation Robustness
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-4-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 4** **Day 5** of the **NgspiceSky130 learning series**!  
This day begins your journey into Analyze the behavior of CMOS inverter is strongly influenced by its **power supply voltage (VDD)**. Changing VDD doesn’t just alter the output voltage swing; it also affects the **switching threshold, noise margins, and overall circuit reliability**. Understanding these effects is crucial for designing robust digital circuits under varying operating conditions using NGSPICE and Sky130 PDK.

---
## 📑 Table of Contents

1. [Static Behavior Evaluation: CMOS Inverter Robustness – Power Supply Variation](#1-static-behavior-evaluation-cmos-inverter-robustness--power-supply-variation)  
   - [Overview](#overview)  
   - [SPICE Simulation Approach](#spice-simulation-approach)  
   - [Key Observations](#key-observations)  
   - [Simulation Observations: Power Supply Variations](#simulation-observations-power-supply-variations)  
   - [Limitations of Operating at Low Supply Voltages](#limitations-of-operating-at-low-supply-voltages)  
   - [CMOS Inverter Robustness to Device Variations](#cmos-inverter-robustness-to-device-variations)  
   - [Sources of Variation: Oxide Thickness](#sources-of-variation-oxide-thickness)  
2. [Labs](#labs)  
   - [Static Behavior Evaluation: CMOS Inverter Robustness, Power Supply Variation](#static-behavior-evaluation-cmos-inverter-robustness-power-supply-variation)  
   - [Simulation 1 - Inverter Device Variation](#simulation-1---inverter-device-variation)  
     - [Step 1: Navigate to the Design Directory](#step-1-navigate-to-the-design-directory)  
     - [Step 2: Inverter Device Variation Analysis](#step-2-inverter-device-variation-analysis)  
     - [Step 3: View the File Contents](#step-3-view-the-file-contents)  
     - [Step 4: Run the Simulation](#step-4-run-the-simulation)  
   - [Simulation 2 - Inverter Supply Voltage Variation](#simulation-2---inverter-supply-voltage-variation)  
     - [Step 1: Inverter Supply Voltage Variation Analysis](#step-1-inverter-supply-voltage-variation-analysis)  
     - [Step 2: View the File Contents](#step-2-view-the-file-contents)  
     - [Step 3: Run the Simulation](#step-3-run-the-simulation-1)  
     - [Table 1: Power Supply Variation (The Voltage Starvation Study)](#-table-1-power-supply-variation-the-voltage-starvation-study)  
3. [Summary: Day 5 – CMOS Inverter Device and Supply Variation Analysis](#summary-day-5--cmos-inverter-device-and-supply-variation-analysis)  
   - [Objective](#objective)  
   - [Activities Performed](#activities-performed)  
   - [Key Observations and Learning Points](#key-observations-and-learning-points)  
   - [Overall Outcome for Day 5](#overall-outcome-for-day-5)  
4. [Repository Info](#repository-info)

---
# **1. Static Behavior Evaluation: CMOS Inverter Robustness – Power Supply Variation**

### Overview
The behavior of a CMOS inverter is strongly influenced by its **power supply voltage (VDD)**. Changing VDD doesn’t just alter the output voltage swing; it also affects the **switching threshold, noise margins, and overall circuit reliability**. Understanding these effects is crucial for designing robust digital circuits under varying operating conditions.

---

### SPICE Simulation Approach
To study this, we ran SPICE simulations of the CMOS inverter while **varying the supply voltage**. Key points of analysis included:  

- Observing how the **Voltage Transfer Characteristic (VTC)** shifts with different VDD levels.  
- Tracking changes in the **switching threshold (VM)**.  
- Calculating **low and high-level noise margins** to evaluate the inverter’s resilience.  

This approach helps visualize how supply scaling impacts both **logic integrity** and **performance**.

---

### Key Observations
1. **Decreasing VDD:**  
   - The switching threshold (**VM**) moves closer to the mid-supply point.  
   - Noise margins shrink, making the inverter **more sensitive to input fluctuations**.  

2. **Impact on Reliability:**  
   - Lower supply voltages reduce **noise immunity**, which can cause logic errors in cascaded stages.  

3. **Increasing VDD:**  
   - The inverter benefits from **larger noise margins**, improving robustness.  
   - However, higher VDD also increases **static power consumption**, highlighting a trade-off between reliability and energy efficiency.  

> By analyzing VTC shifts under different VDD values, we gain insight into **power-performance trade-offs** in CMOS logic, which is critical for low-power and high-reliability designs.

### **Simulation Observations: Power Supply Variations**

1. **Robust Operation at Lower VDD:**  
   - The CMOS inverter operates effectively even at 0.8V, which is below half the original supply voltage (1.8V) and close to the transistor threshold voltages.

2. **Switching Threshold Proportionality:**  
   - With a fixed transistor ratio (*r*), the switching threshold (VM) is approximately proportional to VDD.

3. **Gain in Transition Region:**  
   - The inverter gain in the transition region increases as the supply voltage decreases.

4. **Transition Region Width:**  
   - The width of the transition region reduces when the supply voltage is scaled down from the original VDD.
   ![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203641.png)
   ![Screenshot ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203651.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203704.png)

### **Limitations of Operating at Low Supply Voltages**

1. **Performance Degradation:**  
   - Lowering the supply voltage reduces energy dissipation but significantly increases gate transition times, negatively affecting performance.

2. **Parameter Sensitivity:**  
   - At low supply voltages, the DC characteristics become highly sensitive to variations in device parameters, such as transistor threshold voltages.

3. **Reduced Signal Swing:**  
   - Scaling VDD reduces signal swing, lowering internal noise (e.g., crosstalk) but increasing sensitivity to external noise sources, which do not scale proportionally.
![Screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203717.png)
### **CMOS Inverter Robustness to Device Variations**

1. **Design vs. Real Operating Conditions:**  
   - Gates are designed for nominal conditions, but actual operating temperatures and device parameters can vary widely after fabrication.

2. **DC Characteristics Stability:**  
   - The DC characteristics of the CMOS inverter are relatively insensitive to variations in device parameters, allowing the gate to remain functional across a broad range of operating conditions.

3. **Sources of Variation:**  
   - One common source of variation is the etching process during fabrication, which can affect device parameters.
   ### SOURCES OF ETCHING PROCESS
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203731.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203741.png)

### Sources of variation: Oxide Thickness
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203750.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203801.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203814.png)
![Screenshot 2024-12-14 064317](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203827.png)
![Screenshot 2024-12-14 064325](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203837.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203849.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203859.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203914.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203923.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/Screenshot%202025-10-19%20203938.png)

---

# Labs
## Static behavior evaluation: CMOS inverter robustness, Power supply variation
### Smart SPICE simulation for power supply variations


## Simulation 1 - Inverter Device Variation

### Step 1: Navigate to the Design Directory

We start by moving into the design directory where the Day 5 SPICE files are located:

```
cd sky130CircuitDesignWorkshop/design/
```

This ensures we are in the correct folder for running the SPICE simulations.


### Step 2: Inverter Device Variation Analysis

The simulation file for device variation is:

```
day5_inv_devicevariation_wp7_wn042.spice
```

#### Info about the File Being Simulated

This SPICE file simulates a CMOS inverter while varying transistor sizing to observe its impact on inverter behavior.

**Transistor parameters:**

- *PMOS Width (Wp)* = 7 µm

- *NMOS Width (Wn)* = 0.42 µm

**Purpose:** To study how changes in transistor dimensions affect propagation delay, switching thresholds, and output characteristics.

**Simulation Type:** Transient or DC analysis, depending on the netlist setup.


### Step 3: View the File Contents

We inspect the SPICE netlist using:

```
vim day5_inv_devicevariation_wp7_wn042.spice
```

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/ngspicedevicevariationcode.png)

Open the SPICE file in vim to review transistor sizes, connections, and simulation setup.


### Step 4: Run the Simulation

We run the simulation in NGSPICE:

```
ngspice day5_inv_devicevariation_wp7_wn042.spice
plot out vs in
```

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/ngspicedevicevariationgraph.png)



This produces the input and output waveforms of the inverter, allowing us to observe the effects of device size variation.

### **CMOS Inverter Robustness: Extreme Device Width Variation**

- **Robustness to Width Variation:**  
   The CMOS inverter remains functional even under extreme variations in device width (W) due to its static behavior properties.  

- **Effect on Switching Threshold:**  
   Large deviations in device width primarily affect the switching threshold \( V_M \) but do not drastically impact the inverter's ability to operate as a logic gate.  

- **Impact on Noise Margins:**  
   Variations in width cause asymmetry in the noise margins, with one margin (high or low) reducing while the other increases.

- **Key Insight:**  
   The CMOS inverter exhibits robust static behavior, tolerating extreme width variations without functional failure.


## Simulation 2 - Inverter Supply Voltage Variation


### Step 1: Inverter Supply Voltage Variation Analysis

The simulation file for supply variation is:

```
day5_inv_supplyvariation_Wp1_Wn036.spice
```

#### Info about the File Being Simulated

This SPICE file simulates the CMOS inverter under different supply voltage (VDD) conditions.

**Transistor parameters:**

- *PMOS Width (Wp)* = 1 µm

- *NMOS Width (Wn)* = 0.36 µm

**Purpose:** To observe how VDD variations impact inverter performance, including logic levels, propagation delay, and noise margins.

**Simulation Type:** Transient or DC analysis depending on the netlist.


### Step 2: View the File Contents

We open the netlist in vim:

```
vim day5_inv_supplyvariation_Wp1_Wn036.spice
```

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/ngspicevoltagevariationcode.png)


Letting us review the SPICE file to see the supply voltage setup and inverter configuration.


### Step 3: Run the Simulation

We run the simulation in NGSPICE:

```
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
plot out vs in
```

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/ngspicevoltagevariationgraph.png)

![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day5/Photo/ngspicevoltagevariationgraph1.png)


This generates the inverter waveforms under varying supply voltages, helping us evaluate performance changes.

### ⚡ Table 1: Power Supply Variation (The Voltage Starvation Study)

| VDD (V) | ⚡ VOH (V) | 🛑 VOL (V) | 🔽 VIL (V) | 🔼 VIH (V) | 🔁 Vm (V) | 🟢 NM_L (V) | 🔴 NM_H (V) | Health Status |
|:-------:|:----------:|:----------:|:----------:|:----------:|:---------:|:-----------:|:-----------:|:-------------:|
| **1.8** | 1.800 | ~0 | 0.744 | 0.744 | — | 0.744 | 1.056 | 🟢 Excellent |
| **1.6** | 1.600 | ~0 | 0.687 | 0.687 | 0.791 | 0.687 | 0.913 | 🟢 Good |
| **1.4** | 1.400 | ~0 | 0.621 | 0.787 | 0.700 | 0.621 | 0.613 | 🟡 Marginal |
| **1.2** | 1.200 | ~0 | 0.549 | 0.685 | 0.611 | 0.549 | 0.515 | 🟠 Risky |
| **1.0** | 1.000 | ~0 | 0.482 | 0.594 | 0.531 | 0.482 | 0.406 | 🔴 Danger Zone |
| **0.8** | 0.800 | ~0 | 0.419 | 0.514 | 0.458 | 0.419 | 0.286 | 🔴 Critical |

**📉 Trend Alert**: Notice how noise margins collapse as VDD drops—at 0.8V, you're one hiccup away from logic errors!
---

</div>

**🔍 Key Discoveries:**

- 📉 **Vm Migration**: The switching threshold shifts downward as VDD decreases—like watching the floor drop out from under your logic levels
- 🚨 **Shrinking Safety Margins**: Noise margins compress dangerously, making your circuit more vulnerable to glitches
- 🎯 **VOH Degradation**: High output voltage drops (obviously—you can't get 1.8V out when you only put 0.8V in!)
- ✅ **VOL Stability**: Low output voltage stays rock-solid near ground

---

## Summary: Day 5 – CMOS Inverter Device and Supply Variation Analysis

**Objective:**

- To study how transistor sizing and supply voltage variations affect CMOS inverter behavior.

- To evaluate the impact on propagation delay, switching thresholds, and output levels.

**Activities Performed:**

**Simulation 1: Device Variation (day5_inv_devicevariation_wp7_wn042.spice):**

- Navigated to the design directory.

- Opened the SPICE file in vim to inspect transistor sizing and circuit setup.

- Ran the simulation in NGSPICE and observed the inverter input/output waveforms.

**Simulation 2: Supply Variation (day5_inv_supplyvariation_Wp1_Wn036.spice):**

- Opened the SPICE file in vim to review supply voltage settings and circuit configuration.

- Executed the simulation in NGSPICE and analyzed the inverter response under different VDD values.

**Key Observations and Learning Points:**

- Observed the impact of transistor size on propagation delay and output levels.

- Evaluated how supply voltage variations affect logic levels, rise/fall times, and noise margins.

- Reinforced understanding of CMOS inverter robustness and design considerations for reliable digital circuits.

**Overall Outcome for Day 5:**

- Learned to analyze inverter performance under device and supply variations.

- Gained practical experience in evaluating real-world design tolerances.

- Completed hands-on characterization of CMOS inverters across multiple operating conditions.


---


<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
