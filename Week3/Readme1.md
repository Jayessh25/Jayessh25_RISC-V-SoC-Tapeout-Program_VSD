# Week 3 Part 2:⚡ Static Timing Analysis (STA)

**Static Timing Analysis (STA)** is a fundamental method in **VLSI design** used to verify whether a digital circuit meets its **timing requirements** — ensuring that data moves correctly through flip-flops and logic **at the target clock frequency**.  

Unlike simulation, STA doesn’t rely on input vectors — it checks all possible timing paths **statically**, using cell and wire delay models.

---

## 🧠 Concept Overview

Imagine your digital design as a **race between data and clock**.

- The **clock** decides when data should be captured.
- The **data** passes through a series of logic gates and interconnects (each adding delay).
- STA verifies that data reaches the destination **at the right time** and **stays stable long enough**.

There are two main checks:
1. **Setup check** → Is data fast enough?
2. **Hold check** → Is data not too fast?

✅ Timing met → circuit runs safely  
❌ Timing violated → chip may fail at speed

---

## 🧩 Why We Use STA

| **Reason** | **Description** |
|-------------|----------------|
| **Vectorless verification** | No need for testbench or simulation vectors. |
| **Comprehensive coverage** | All paths are analyzed — even rare cases missed by simulation. |
| **Speed and scalability** | Works for multi-million gate designs. |
| **Corner-based analysis** | Checks timing across process, voltage, and temperature (PVT) corners. |
| **Signoff standard** | Used before fabrication to guarantee functionality and speed. |

---

## 🧱 STA Workflow (Simplified)

Netlist (post-synthesis or post-layout)
↓
Cell & Wire Delay Models (.lib, .spef)
↓
Clock Constraints (SDC)
↓
Static Timing Analyzer (OpenSTA / PrimeTime)
↓
Timing Reports → Setup/Hold Slack → Pass/Fail

yaml
Copy code

---

## 🕒 Setup and Hold Time Checks

### 🧩 Setup Check
Ensures data **arrives early enough** before the next clock edge.

Clock Period ≥ (T_clk→Q + T_comb + T_setup + Skew)

mathematica
Copy code

If violated → setup violation → reduce frequency or optimize logic.

### ⏱️ Hold Check
Ensures data **doesn’t arrive too early** after the clock edge.

T_clk→Q + T_comb ≥ T_hold + Skew

pgsql
Copy code

If violated → hold violation → add delay or balance skew.

---

## 🧮 Key Timing Parameters

| **Parameter** | **Meaning** | **Notes** |
|----------------|-------------|-----------|
| **Clock-to-Q (T_clk→Q)** | Delay from clock edge to data output of flip-flop | Starting point for path |
| **Combinational Delay (T_comb)** | Delay through logic gates/wires | Affected by load, fanout |
| **Setup Time (T_setup)** | Data must be stable *before* clock edge | Ensures proper latching |
| **Hold Time (T_hold)** | Data must be stable *after* clock edge | Prevents data corruption |
| **Clock Skew** | Difference in clock arrival time between FFs | Can be useful or harmful |
| **Slack** | `Required – Arrival` | Positive → safe, Negative → violation |

---

## 🧩 STA Path Types

| **Type** | **Start Point** | **End Point** | **Typical Check** |
|-----------|----------------|---------------|-------------------|
| **Reg → Reg** | FF → Logic → FF | Setup / Hold |
| **In → Reg** | Input port → Logic → FF | Setup |
| **Reg → Out** | FF → Logic → Output port | Setup |
| **In → Out** | Input → Logic → Output | Combinational path |
| **Clock Gating** | Clock → Enable path | Glitch-free gating |
| **Recovery/Removal** | Async reset/set → Clock | Reset timing |
| **Latch Timing** | Latch-based designs | Time borrow/give |
| **Data-to-Data** | Internal dependent signals | Data race analysis |

---

## ⚙️ Detailed Timing Path Equation

For **setup check**:

Data Arrival Time ≤ Data Required Time

Data Arrival Time = Launch Clock + T_clk→Q + T_comb
Data Required Time = Capture Clock + T_setup
Slack = (Required – Arrival)

sql
Copy code

For **hold check**:

Data Arrival Time ≥ Data Required Time

Data Arrival Time = Launch Clock + T_clk→Q + T_comb
Data Required Time = Capture Clock + T_hold
Slack = (Arrival – Required)

yaml
Copy code

---

## ⚡ Slew / Transition Analysis

| **Type** | **Check** | **Purpose** |
|-----------|------------|-------------|
| **Data Slew (max/min)** | Max: avoid slow transitions <br> Min: avoid too fast switching | Prevent distortion and noise |
| **Clock Slew (max/min)** | Similar checks for clock signals | Ensure clock integrity |

> Slow slew = poor signal integrity → increased delay  
> Fast slew = potential crosstalk/glitch issues  

---

## ⚙️ Load Analysis

| **Type** | **Definition** | **Reason for Check** |
|-----------|----------------|----------------------|
| **Fanout (max/min)** | Number of driven inputs per output | Avoid overloading driver |
| **Capacitance (max/min)** | Total load (wire + input cap) | Controls transition time |

---

## ⏱️ Clock Analysis

| **Parameter** | **Definition** | **Impact** |
|----------------|----------------|------------|
| **Clock Skew** | Difference in clock arrival between FFs | Affects setup/hold margin |
| **Clock Jitter** | Variation in clock edge over cycles | Adds timing uncertainty |
| **Pulse Width** | Duration of high/low clock level | Prevents narrow glitches |
| **Duty Cycle** | Ratio of high to total period | Impacts edge-based timing |

---

## 🧩 Advanced STA Topics

| **Concept** | **Description** |
|--------------|----------------|
| **AOCV (Advanced OCV)** | Accounts for distance-dependent variation in delay |
| **POCV (Parametric OCV)** | Statistical timing variation modeling |
| **MCMM (Multi-Corner Multi-Mode)** | Analyzes multiple PVT corners and operating modes simultaneously |
| **CRPR (Clock Reconvergence Pessimism Removal)** | Avoids double-counting common clock delays |
| **ECO (Engineering Change Order)** | Fixes setup/hold violations after layout without full re-synthesis |
| **Timing Exceptions** | `set_false_path`, `set_multicycle_path`, etc., to ignore non-critical paths |
| **Derating** | Scaling delays to account for process/voltage uncertainty |
| **Clock Tree Synthesis (CTS)** | Balances skew and minimizes latency |
| **Signoff STA** | Final check before GDSII tapeout |

---

## 📈 STA Process in Real Flow

1. **Pre-Layout STA (Post-Synthesis)**  
   - Uses ideal clocks and estimated wire delays.  
   - Goal: Catch logic-level timing issues early.  

2. **Post-Layout STA (Post-Route)**  
   - Uses extracted parasitics (`.spef`).  
   - Realistic wire delay and coupling considered.  

3. **Signoff STA**  
   - Corner-based (SS/FF/TT, Vmin/Vmax, Tmin/Tmax).  
   - Ensures chip works across all PVT conditions.  

---

## 🧰 Tools for STA

| **Tool** | **Vendor / Type** | **Usage** |
|-----------|------------------|-----------|
| **OpenSTA** | Open-Source | Static timing verification |
| **PrimeTime** | Synopsys | Industry-standard STA tool |
| **Tempus** | Cadence | Multi-corner signoff STA |
| **GoldTime** | Siemens | Static & dynamic timing |
| **OpenTimer** | Lightweight open-source | Fast path analysis |

---

## 📘 STA Report Example

Startpoint: U1/FF1/Q
Endpoint: U2/FF2/D
Path Type: Setup

Clock: CLK (rise edge)
Point Arrival Required Slack
Clock Launch Edge 0.000
U1/FF1 (Clk→Q) 0.120
Combinational Delay 1.520
Clock Capture Edge 2.000
U2/FF2 (Setup) 0.120
Slack = Required - Arrival = 2.000 - 1.640 = +0.360 ns

yaml
Copy code

✅ Positive slack → timing met  
❌ Negative slack → timing violation

---

## 🏁 STA Signoff Checklist

| **Check** | **Goal** |
|------------|----------|
| Setup timing | Meets frequency requirement |
| Hold timing | No data corruption |
| Slew check | Signal integrity within limits |
| Capacitance/fanout | Driver load acceptable |
| Clock skew/jitter | Within skew budget |
| DRC/LVS clean | Physical correctness |
| Power/timing co-validation | Balanced design |
| STA @ all corners | Fully robust SoC ready for tapeout |

---

> 🧠 **Summary:**  
> STA is the *heartbeat verification* of digital design — it ensures every bit of data races safely with the clock, across every gate, path, and condition.

> 🛠️ *If logic defines “what” a chip does, STA defines “how fast and safely” it can do it.
