# ⚙️ Part 1 — Post Synthesis GLS: “Bringing BabySoC to Life”

Welcome to **Part 1 of Week 3!** 🚀  
Up to now, your BabySoC has lived in the world of RTL — abstract, fast, and ideal.  
But in the real silicon world, wires have delays, gates have timing, and logic must keep up with the clock.  

This is where **Gate-Level Simulation (GLS)** steps in — it’s like giving your design its first **heartbeat** after synthesis.  
We move from ideal logic to the actual gate-level representation, ensuring that every flip-flop and signal transition behaves correctly when timing becomes real. 💡  

---

## 🧠 What Is GLS?

**Gate-Level Simulation (GLS)** is the stage where your synthesized design is tested in its true hardware form.  
After synthesis, your design turns into a **netlist** — a file describing how real logic gates are connected.  
GLS takes this netlist and runs simulations to verify that functionality is preserved **and** that timing behaves as expected.

Unlike RTL simulation:
- GLS includes **propagation delays** from real hardware.
- The testbench remains the same, but the underlying logic now mirrors **post-synthesis reality**.
- It helps detect issues that may not appear at the RTL level.

---

## 🔍 Why BabySoC Needs GLS

Your **BabySoC** integrates multiple modules — the RISC-V core, PLL, DAC, and more.  
Each has its own timing behavior and clock domain. GLS ensures that when these modules talk to each other, they **stay synchronized** and **stable** under real timing constraints.  

Here’s what GLS validates:
- ⏱️ **Timing Consistency:** Signals meet setup and hold times defined in the Standard Delay Format (SDF).  
- ⚡ **Functional Integrity:** The synthesized design still performs the intended operations.  
- 🧩 **Inter-Module Interaction:** No unexpected glitches or metastability during communication between components.

---

## 🧰 Tools You’ll Use

To bring your BabySoC to life at the gate level, you’ll work with:

- 🧠 **Icarus Verilog** — for compiling and simulating the gate-level netlist.  
- 🌈 **GTKWave** — for waveform visualization and debugging timing delays.  
- 📄 **SDF Files** — to inject real-world delay data into your simulations.  

These tools together allow you to view how your design transitions from **“logical correctness”** to **“hardware readiness.”**

---

## 🧭 The Real Purpose

Think of GLS as the **truth test** for synthesis.  
It answers one simple but critical question:

> “Does my design still behave correctly when it becomes hardware?”

When you see your BabySoC waveforms align perfectly under timing delays — that’s your moment of validation. ✨  

---

## 💬 In a Nutshell

> “Gate-Level Simulation is where your SoC stops being an idea and starts acting like a chip —  
> precise, timed, and ready for silicon.” ⚙️  

---

Here is the step-by-step execution plan for running the  commands manually:
---
### **Step 1: Load the Top-Level Design and Supporting Modules**
```bash
yosys
```

![Screenshot from ](). 


Inside the Yosys shell, run:
```yosys
read_verilog /home/jaynandan/vsd/VLSI/VSDBabySoC/src/module/vsdbabysoc.v
read_verilog -sv -I /home/jaynandan/vsd/VLSI/VSDBabySoC/src/include /home/jaynandan/vsd/VLSI/VSDBabySoC/src/module/rvmyth.v
read_verilog -I /home/jaynandan/vsd/VLSI/VSDBabySoC/src/include /home/jaynandan/vsd/VLSI/VSDBabySoC/src/module/clk_gate.v

```
![Screenshot from ](). 

---

### **Step 2: Load the Liberty Files for Synthesis**
Inside the same Yosys shell, run:
```yosys
read_liberty -lib  /home/jaynandan/vsd/VLSI/VSDBabySoC/src/lib/avsdpll.lib
read_liberty -lib  /home/jaynandan/vsd/VLSI/VSDBabySoC/src/lib/avsddac.lib
read_liberty -lib  /home/jaynandan/vsd/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![WhatsApp Image ]()

---

### **Step 3: Run Synthesis Targeting `vsdbabysoc`**
```yosys
synth -top vsdbabysoc
```
![WhatsApp Image ]()
![WhatsApp Image ]()
![WhatsApp Image )]()
![WhatsApp Image ]()
![WhatsApp Image ]()


---

### **Step 4: Map D Flip-Flops to Standard Cells**
```yosys
dfflibmap -liberty /home/jaynandan/vsd/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![WhatsApp Image )]()

---

### **Step 5: Perform Optimization and Technology Mapping**
```yosys
opt
abc -liberty /home/jaynandan/vsd/VLSI/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
![WhatsApp Image ]()
![WhatsApp Image ]()

---

### **Step 6: Perform Final Clean-Up and Renaming**
```yosys
flatten
setundef -zero
clean -purge
rename -enumerate
```
![WhatsApp Image ]()

---

### **Step 7: Check Statistics**
```yosys
stat
```
![WhatsApp Image ]()
![WhatsApp Image ]()


---

### **Step 8: Write the Synthesized Netlist**
```yosys
write_verilog -noattr /home/jaynandan/vsd/VLSI/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
```
![WhatsApp Image ]()

---

## POST_SYNTHESIS SIMULATION AND WAVEFORMS
---

### **Step 1: Compile the Testbench & Run Simulation**
Run the following `iverilog` command to compile the testbench and genrate the .vcd file:
```bash
iverilog -o /home/jaynadan/vsd/VLSI/VSDBabySoC/output/post_synth_sim/post_synth_sim.out \
-DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 \
-I /home/jaynadan/vsd/VLSI/VSDBabySoC/src/include \
-I /home/jaynadan/vsd/VLSI/VSDBabySoC/src/module \
-I /home/jaynadan/vsd/VLSI/VSDBabySoC/output/synth \
/home/jaynadan/vsd/VLSI/VSDBabySoC/src/module/testbench.v

vvp /home/jaynadan/vsd/VLSI/VSDBabySoC/output/post_synth_sim/post_synth_sim.out

```

---
### **Step 2: View the Waveforms in GTKWave**

```bash
gtkwave post_synth_sim.vcd
```
---

![WhatsApp Image ]()

![WhatsApp Image]()

![WhatsApp Image )]()

![WhatsApp Image ]()
