# **Static Behavior Evaluation: CMOS Inverter Robustness and Noise Margin**

### Understanding Noise Margin
In digital circuits, **noise is inevitable** — small voltage spikes or fluctuations can sneak in due to interference, crosstalk, or power supply variations. A CMOS inverter’s **noise margin** defines its ability to **tolerate these disturbances** without misinterpreting a logic level. In simpler terms, it’s the **safety buffer** that ensures a '1' stays a '1' and a '0' stays a '0', even in the presence of noise.  

If the noise is smaller than this margin, the inverter naturally **filters it out**, letting clean signals propagate through multiple logic stages without causing errors.

---

### Why Noise Margin Matters
Noise margin is essential for **robust digital operation**, especially when connecting several gates in series:  

- A logic-high signal (close to **VDD**) with small noise must still be recognized as **logic 1**.  
- A logic-low signal (close to **0 V**) with small noise must still be recognized as **logic 0**.  

By ensuring these conditions, CMOS circuits maintain **reliability** and **prevent logic corruption** in complex digital systems.

---

### Visualizing Noise Margin with VTC
Consider **two CMOS inverters connected back-to-back**. The **Voltage Transfer Characteristic (VTC)** of the first inverter determines the input range where the second inverter interprets signals correctly.  

- **Ideal VTC:** Extremely sharp transition from low to high output, offering maximum noise immunity.  
- **Piece-wise Linear VTC:** Simplified approximation highlighting critical switching points.  
- **Realistic VTC:** Real-world simulation showing gradual transitions and non-ideal behavior.  

![Noise Margin Illustration](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194220.png)

---

### Key Points: VIL and VIH
The **VTC curve** contains critical points, **VIL** (Input Low Voltage) and **VIH** (Input High Voltage), which define the inverter’s **noise tolerance boundaries**:

- **Definition:**  
  - **VIL** and **VIH** are the input voltages where the slope of the VTC equals **-1**, meaning the inverter’s gain magnitude is unity.  
- **Logic Interpretation:**  
  - **Vin ≤ VIL** → Output clearly recognized as **logic 0**.  
  - **Vin ≥ VIH** → Output clearly recognized as **logic 1**.  

> These points effectively separate the **safe regions** from the **undefined zone**, where small input variations can cause output uncertainty.

---

### **Noise Margin: VIL and VIH Points**

- **Definition:**  
  - **VIL** and **VIH** are operational points where the slope of the voltage transfer curve (VTC) is **-1**.  
  - These points correspond to the gain of the inverter being equal to **-1**.  

- **Logic Recognition:**  
  - Input voltage **0 to VIL** → Recognized as **Logic 0**.  
  - Input voltage **VIH to VDD** → Recognized as **Logic 1**.
   ### **Noise Margins in CMOS Logic Gates**

1. **Conditions for Proper Operation:**  
   - The following conditions must hold for a logic gate to function correctly in the presence of noise:  
     ```
     VOL_MAX < VIL_MAX  
     VOH_MIN > VIH_MIN  
     ```  

2. **Behavior in Different Input Ranges:**  
   - **For Vin ≤ VIL**:  
     The inverter gain magnitude is less than unity, resulting in minimal output change for a given input change.  
   - **For Vin ≥ VIH**:  
     The gain magnitude is also less than unity, leading to minimal output change.  
   - **For VIL < Vin < VIH**:  
     The gain magnitude exceeds unity, causing significant output changes. This range is called the **undefined region** because noise signals pushing Vin into this range may introduce errors.  

3. **Noise Margin Equations:**  
   - **Low-Level Noise Margin (NML):**  
     ```
     NML = VIL_MAX - VOL_MAX  
     ```  
   - **High-Level Noise Margin (NMH):**  
     ```
     NMH = VOH_MIN - VIH_MIN  
     ```  
   - **Overall Noise Margin (NM):**  
     ```
     NM = Min(NML, NMH)  
     ```  

These noise margins define the tolerance of a circuit to noise without compromising its logical operation.
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194234.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194252.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194302.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194312.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194321.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194331.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194341.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194353.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194424.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194436.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194452.png)
![screenshot](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/Screenshot%202025-10-19%20194510.png)

# LABS
##  Static behavior evaluation: CMOS inverter robustness, Noise margin
##### Noise Margin - sky130 Inverter (Wp/Lp=1u/0.15u, Wn/Ln=0.36u/0.15u)
```
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```

 ![ Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/ngspicenoicemarginwave.png)
 ![ Image ](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week4/Day4/Photo/ngspicenoicemarginwavemidpoint.png)
