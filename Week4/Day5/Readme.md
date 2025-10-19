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
   ![Screenshot ]()
   ![Screenshot ]()
![screenshot]()

### **Limitations of Operating at Low Supply Voltages**

1. **Performance Degradation:**  
   - Lowering the supply voltage reduces energy dissipation but significantly increases gate transition times, negatively affecting performance.

2. **Parameter Sensitivity:**  
   - At low supply voltages, the DC characteristics become highly sensitive to variations in device parameters, such as transistor threshold voltages.

3. **Reduced Signal Swing:**  
   - Scaling VDD reduces signal swing, lowering internal noise (e.g., crosstalk) but increasing sensitivity to external noise sources, which do not scale proportionally.
![Screenshot]()
### **CMOS Inverter Robustness to Device Variations**

1. **Design vs. Real Operating Conditions:**  
   - Gates are designed for nominal conditions, but actual operating temperatures and device parameters can vary widely after fabrication.

2. **DC Characteristics Stability:**  
   - The DC characteristics of the CMOS inverter are relatively insensitive to variations in device parameters, allowing the gate to remain functional across a broad range of operating conditions.

3. **Sources of Variation:**  
   - One common source of variation is the etching process during fabrication, which can affect device parameters.
   ### SOURCES OF ETCHING PROCESS
![screenshot]()
![screenshot]()
![screenshot]()
![screenshot]()
![screenshot]()
### Sources of variation: Oxide Thickness
![screenshot]()
![screenshot]()
![screenshot]()
![Screenshot 2024-12-14 064317]()
![Screenshot 2024-12-14 064325]()
![screenshot]()
![screenshot]()
![screenshot]()
![screenshot]()
![screenshot]()

## Static behavior evaluation: CMOS inverter robustness, Power supply variation
### Smart SPICE simulation for power supply variations

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

.control

let powersupply = 1.8
alter Vdd = powersupply
	let voltagesupplyvariation = 0
	dowhile voltagesupplyvariation < 6
	dc Vin 0 1.8 0.01
	let powersupply = powersupply - 0.2
	alter Vdd = powersupply
	let voltagesupplyvariation = voltagesupplyvariation + 1
      end
 
plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc

```

![ Image ]()
![ Image ]()



### **CMOS Inverter Robustness: Extreme Device Width Variation**

- **Robustness to Width Variation:**  
   The CMOS inverter remains functional even under extreme variations in device width (W) due to its static behavior properties.  

- **Effect on Switching Threshold:**  
   Large deviations in device width primarily affect the switching threshold \( V_M \) but do not drastically impact the inverter's ability to operate as a logic gate.  

- **Impact on Noise Margins:**  
   Variations in width cause asymmetry in the noise margins, with one margin (high or low) reducing while the other increases.

- **Key Insight:**  
   The CMOS inverter exhibits robust static behavior, tolerating extreme width variations without functional failure.

  
```
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=7 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.42 l=0.15


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
![screenshot]()
![screenshot]()
![screenshot]()
