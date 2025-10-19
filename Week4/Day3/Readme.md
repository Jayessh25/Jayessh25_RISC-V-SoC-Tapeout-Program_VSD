# 1. Voltage Transfer Characteristics: SPICE Simulations

To explore the behavior of a **CMOS inverter** in SPICE, careful attention to circuit setup and parameters is essential. Think of it as **building a miniature digital world**, where every connection and value matters:

- **Connecting Components:** Ensure that **PMOS, NMOS, Vdd, Vss, Vin, and Vout** are correctly wired, so the inverter performs its switching magic.  
- **Defining Component Values:** Set the stage with accurate parameters — include **transistor sizes, threshold voltages (Vth), and supply voltage (Vdd)** — to make the simulation reflect real-world behavior.  
- **Identifying Nodes:** Map out every electrical node, such as **input (Vin), output (Vout), drain, source, and bulk terminals**, so you know exactly where the action is happening.  
- **Naming Nodes:** Give every node a clear, descriptive name (like **Vout** for the output) to make analysis intuitive and tracking results effortless.  

> By thoughtfully connecting and naming every piece of the circuit, SPICE becomes a powerful microscope to visualize how the inverter switches between logic states.

![Screenshot]()
![SCREESHOT]()
### SPICE simulation for CMOS inverter
SPICE Netlist for CMOS Inverter
A **SPICE netlist** for a CMOS inverter includes transistor models, power supply connections, input voltage source, transistor specifications (PMOS and NMOS), and simulation commands (e.g., `.tran` for transient analysis).
![Screenshot]()
Same Wn/Ln = Wp/Lp = 1.5. Plot out vs in:
![Screenshot ]()
Now, Wn/Ln = 1.5 and Wp/Lp = 3.75. Plot out vs in:
![Screenshot]()
![screen]()
## **Static Behavior Evaluation: CMOS Inverter Robustness and Switching Threshold (Vm)**  
The **switching threshold (Vm)** is the point where **Vin = Vout**, and both transistors are in saturation (since **Vds = Vgs**). At **Vm**, maximum power is drawn due to large current, and it can be graphically found at the intersection of the **VTC** with the **Vin = Vout** line. The analytical expression for **Vm** is obtained by equating the drain currents of PMOS and NMOS (**IDSn = IDSp**).
![Screenshot ]()


In the **velocity-saturated** case, the **switching threshold (Vm)** is the point where both **NMOS** and **PMOS** transistors are in saturation, and the drain currents are equal. This occurs when the **VDS** of both devices is less than the saturation voltage, i.e., **VDSAT < (Vm − VT)**. The threshold voltage **Vm** can be derived by equating the drain currents of both transistors, with the device widths and lengths (W/L ratios) playing a key role in determining the point where both transistors conduct equally.
![Screenshot ]()
![Screenshot]()
![Screenshot ]()
![Screenshot ]()
![Screenshot ]()
![Screenshot]()
![screenshot]()


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

![Screenshot ]()
![screenshot]()
![screenshot]()
### Applications of CMOS inverter in clock network and STA
![Screenshot ]()
![Screenshot ]()

# LABS
## 1. Voltage transfer characteristics: SPICE simulations
#### Sky130 SPICE simulation for CMOS - VTC
     *Model Description
     .param temp=27

      *Including sky130 library files
      .lib "sky130_fd_pr/models/sky130.lib.spice" tt

      *Netlist Description
       XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
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
        
![ Image ]()
![ Image ]()

- **Switching Threshold (VM):**  
  The input voltage (**Vin**) at which the output voltage (**Vout**) equals the input voltage.  
  - It is the intersection point on the Voltage Transfer Curve (VTC).  
  - Also referred to as the **midpoint voltage** of the CMOS inverter.

  ### Sky130 SPICE simulation for CMOS - Transient Analysis
      *Model Description
       .param temp=27

      *Including sky130 library files
        .lib "sky130_fd_pr/models/sky130.lib.spice" tt

        *Netlist Description
         XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
         XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15
         Cload out 0 50fF
         Vdd vdd 0 1.8V
         Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

         *simulation commands
         .tran 1n 10n
         .control
          run
          .endc
          .end
  
  ![ Image ]()
  ![ Image ]()
  ![Image]()

  # Labs On Day 3


## Simulation 1 - Transient analysis


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




Open the netlist in vim so we can review transistor connections, input waveform, and transient analysis commands.


### Step 4: Run the Transient Simulation

We execute the simulation in NGSPICE:

```
ngspice day3_inv_tran_Wp084_Wn036.spice
plot out vs in
```



This generates the input and output waveforms of the CMOS inverter, allowing us to measure rise/fall times and propagation delay.





## Simulation 2 - Voltage Transfer Chracteristics (VTC)

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




Allowing us to see the DC sweep setup, transistor sizing, and output node definition.


### Step 3: Run the VTC Simulation

We run the simulation in NGSPICE:

```
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs time in
```



This produces the Vout vs Vin curve, from which we can determine the inverter’s switching threshold and noise margins.




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
