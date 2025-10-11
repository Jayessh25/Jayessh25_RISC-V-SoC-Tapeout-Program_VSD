# ⚡ Static Timing Analysis (STA)

**Static Timing Analysis (STA)** is a method used to check if a digital circuit will operate correctly at the required clock frequency — **without needing to simulate it with all possible input combinations**.

It is one of the most important steps in **VLSI design** (used after synthesis and place & route).

---

## 🧠 Simple Understanding

Think of your digital circuit like a **race between data and the clock**:

- The **clock** says when data must be ready.
- The **data** takes some time to travel through gates and wires.

STA checks:

> “Will the data reach the destination **before the clock asks for it (setup check)**?  
> And will it **stay stable long enough after the clock edge (hold check)**?”

✅ **If both are true → Timing met**  
❌ **If not → Timing violation**

---

## 🎯 Why We Use STA

| **Reason** | **Explanation** |
|-------------|----------------|
| **Speed** | STA is much faster than dynamic (simulation-based) timing analysis because it doesn’t require test vectors. |
| **Coverage** | It checks **all possible paths** in the design — no need to simulate every input combination. |
| **Accuracy** | Considers realistic factors: cell delay, wire delay, clock skew, setup/hold times, etc. |
| **Signoff** | Before tape-out, STA ensures the chip can run at its target frequency (like 500 MHz or 1 GHz). |

🧩 **In short — STA guarantees your chip works at the desired speed under all conditions.**

---

## 🧩 What STA Does

STA calculates timing for all paths between:

| **Type** | **From** | **To** | **Example** |
|-----------|-----------|--------|--------------|
| 1️⃣ Reg-to-Reg | Flip-flop | Flip-flop | Common pipeline path |
| 2️⃣ In-to-Reg | Input port | Flip-flop | External input to internal logic |
| 3️⃣ Reg-to-Out | Flip-flop | Output port | Internal logic to output |
| 4️⃣ In-to-Out | Input port | Output port | Combinational logic only |

Then, it checks two main conditions for each path:

---

### 🕒 1. Setup Check
Ensures data **arrives before** the next clock edge.  
Violations cause **setup timing failure** → chip can’t run at high frequency.

**Example:**  
Data must arrive before next clock edge (10 ns period)  
If data arrives at 10.5 ns → setup violation ❌

---

### ⏱️ 2. Hold Check
Ensures data **doesn’t change too soon** after the clock edge.  
Violations cause **hold timing failure** → may latch wrong data.

**Example:**  
Data must remain stable for 0.2 ns after clock edge  
If data changes at 0.1 ns → hold violation ❌

---

## 🧮 Key Terms in STA

| **Term** | **Meaning** |
|-----------|-------------|
| **Arrival Time** | When the data actually reaches a node |
| **Required Time** | When data *should* reach to meet timing |
| **Slack = Required – Arrival** | Timing margin (positive = good, negative = violation) |
| **Setup Time** | Time data must be stable *before* clock edge |
| **Hold Time** | Time data must be stable *after* clock edge |
| **Clock Skew** | Difference in clock arrival time at different flip-flops |
| **Jitter** | Variation in clock edge timing |
| **OCV (On-Chip Variation)** | Delay difference due to process variations |

---

## 🧰 Tools Used for STA

| **Tool** | **Purpose** |
|-----------|-------------|
| **OpenSTA** | Performs static timing analysis |
| **OpenROAD / TritonCTS** | Handles clock tree synthesis & timing optimization |
| **Timing Reports** | Generated after synthesis/place & route |

---

## 🧩 Timing Analysis Terms Explained

### 1️⃣ Arrival Time
Actual time at which a signal reaches a node.  
🕒 *When the data actually arrives.*

---

### 2️⃣ Required Time
Latest (for setup) or earliest (for hold) time data must arrive.  
🕕 *When the data is supposed to arrive.*

---

### 3️⃣ Slack
`Slack = Required Time – Arrival Time`  
- **Positive Slack** → Timing met ✅  
- **Negative Slack** → Timing violation ❌

---

### 4️⃣ GBA – Graph-Based Analysis
- Computes timing once per node (fast but pessimistic).  
⚡ *Faster but slightly less accurate.*

---

### 5️⃣ PBA – Path-Based Analysis
- Computes timing per path (accurate but slower).  
🎯 *More accurate, path-by-path timing.*

---

### 6️⃣ Clk-to-Q Delay
Time taken by a flip-flop to reflect a change at its output after a clock edge.  
🔁 *Start point delay of every sequential path.*

---

### 7️⃣ Library Setup and Hold Time
Defined in `.lib` for each flip-flop.  
📚 *Determines timing window for data.*

---

### 8️⃣ Jitter
Clock edge variation due to noise or instability.  
📉 *Clock “wobble” that affects precision.*

---

### 9️⃣ On-Chip Variation (OCV)
Represents process variations across the same chip.  
⚙️ *Accounts for real-world path delay differences.*

---

### 🔟 Pessimism Removal
Removes double-counting of shared clock paths between launch and capture points.  
🔧 *Results in more realistic timing numbers.*

---

## ✅ Quick Summary Table

| **Term** | **Meaning** | **Key Idea** |
|-----------|--------------|---------------|
| Arrival Time | When the signal actually reaches the destination | Actual timing |
| Required Time | When the signal should arrive | Expected timing |
| Slack | Required – Arrival | Timing margin |
| GBA | Graph-based timing | Fast, pessimistic |
| PBA | Path-based timing | Slow, accurate |
| Clk-to-Q | FF output delay after clock | Start-point delay |
| Library Setup/Hold | Min data stable times | Cell timing constraint |
| Jitter | Clock edge variation | Reduces margin |
| On-Chip Variation | Delay variation across chip | Adds derate factors |
| Pessimism Removal | Removes duplicate delays | More accurate timing |

---

## 🕒 Setup and Hold Time Analysis

| **Type** | **Path** | **Description** |
|-----------|-----------|----------------|
| **1️⃣ Reg-to-Reg** | Flip-flop → Logic → Flip-flop | Most common; checks setup & hold timing |
| **2️⃣ In-to-Reg** | Input → Logic → Flip-flop | Ensures external signals meet timing |
| **3️⃣ Reg-to-Out** | Flip-flop → Logic → Output | Ensures output is ready for next device |
| **4️⃣ In-to-Out** | Input → Logic → Output | Pure combinational path check |
| **5️⃣ Clock Gating** | Clock → Gating enable | Prevents glitches in gated clocks |
| **6️⃣ Recovery/Removal** | Async reset/set → Clock | Ensures reset timing correctness |
| **7️⃣ Data-to-Data** | Data1 → Data2 | Validates dependent signal timing |
| **8️⃣ Latch Timing** | Latch paths | Time borrow/give analysis for latches |

---

## ⚡ Slew / Transition Analysis

Slew = time for a signal to transition (rise/fall) between 10%–90% voltage.

| **Type** | **Check** | **Purpose** |
|-----------|------------|--------------|
| **Data Slew (max/min)** | Max: avoid slow transitions <br> Min: avoid too fast transitions | Ensure good signal integrity |
| **Clock Slew (max/min)** | Similar to data but for clock | Maintain clock edge quality |

---

## ⚙️ Load Analysis

| **Type** | **Definition** | **Purpose** |
|-----------|----------------|--------------|
| **Fanout (max/min)** | Number of inputs driven by one output | Avoid overloading a driver |
| **Capacitance (max/min)** | Total load (wire + input cap) | Prevent slow transitions |

---

## ⏱️ Clock Analysis

| **Parameter** | **Definition** | **Effect** |
|----------------|----------------|-------------|
| **Clock Skew** | Difference in clock arrival time between two FFs | Positive skew → helps setup, hurts hold <br> Negative skew → hurts setup, helps hold |
| **Pulse Width** | Duration of active clock level | Prevents narrow pulses or double clocking |

---

## 🧩 Summary Table

| **Category** | **Type** | **Purpose** |
|---------------|-----------|--------------|
| **Setup/Hold** | Reg2Reg, In2Reg, Reg2Out, In2Out | Data timing w.r.t. clock |
|  | Clock Gating | Gated clock timing |
|  | Recovery/Removal | Async timing |
|  | Data-to-Data | Data dependency |
|  | Latch Timing | Time borrow/give |
| **Slew** | Data/Clock (max/min) | Signal transition check |
| **Load** | Fanout/Capacitance | Driver load check |
| **Clock** | Skew, Pulse Width | Clock quality & stability |

---

> 💡 **STA = The silent guardian of chip timing.**  
> It ensures every path meets its setup and hold timing — so your chip runs safely at full speed ⚙️

