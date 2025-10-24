# 🚀 Week 4 Day 1: NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-4-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Week 4** **Day 1** of the **NgspiceSky130 learning series**!  
This day begins your journey into **transistor-level design and SPICE simulation** using the open-source **Sky130 PDK**.  
We’ll explore the electrical behavior of the **NMOS transistor** and understand how **drain current (Id)** varies with **drain-to-source voltage (Vds)** under different gate bias conditions. Day 1 — NMOS Drain Current (Id) vs Drain-to-Source Voltage (Vds)

---
# ⚙️ Part 1 — Post Synthesis GLS: “Bringing BabySoC to Life”

Welcome to **Part 1 of Week 3!** 🚀  
Up to now, your BabySoC has lived in the world of RTL — abstract, fast, and ideal.  
But in the real silicon world, wires have delays, gates have timing, and logic must keep up with the clock.  

This is where **Gate-Level Simulation (GLS)** steps in — it’s like giving your design its first **heartbeat** after synthesis.  
We move from ideal logic to the actual gate-level representation, ensuring that every flip-flop and signal transition behaves correctly when timing becomes real. 💡  

---

### 📚 Contents

- [Overview](#vsdbabysoc-post-synthesis-simulation)
- [Step 1: Load the Top-Level Design and Supporting Modules](#step-1-load-the-top-level-design-and-supporting-modules)
- [Step 2: Load the Liberty Files for Synthesis](#step-2-load-the-liberty-files-for-synthesis)
- [Step 3: Run Synthesis Targeting `vsdbabysoc`](#step-3-run-synthesis-targeting-vsdbabysoc)
- [Step 4: Map D Flip-Flops to Standard Cells](#step-4-map-d-flip-flops-to-standard-cells)
- [Step 5: Perform Optimization and Technology Mapping](#step-5-perform-optimization-and-technology-mapping)
- [Step 6: Perform Final Clean-Up and Renaming](#step-6-perform-final-clean-up-and-renaming)
- [Step 7: Check Statistics](#step-7-check-statistics)
- [Step 8: Write the Synthesized Netlist](#step-8-write-the-synthesized-netlist)
- [POST_SYNTHESIS SIMULATION AND WAVEFORMS](#post_synthesis-simulation-and-waveforms)
  - [Step 1: Compile the Testbench](#step-1-compile-the-testbench)
  - [Step 2: Navigate to the Post-Synthesis Simulation Output Directory](#step-2-navigate-to-the-post-synthesis-simulation-output-directory)
  - [Step 3: Run the Simulation](#step-3-run-the-simulation)
  - [Step 4: View the Waveforms in GTKWave](#step-4-view-the-waveforms-in-gtkwave)
- [Comparing Pre-Synthesis and Post-Synthesis Output](#comparing-pre-synthesis-and-post-synthesis-output)
    

## POST-SYNTHESIS SIMULATION

### 🧪 Gate-Level Simulation (GLS) – Post-Synthesis Verification
- **Gate-Level Simulation** (GLS), also known as **Post-Synthesis Simulation**, is performed to validate the **functional and timing correctness of a design after synthesis**. 
- While RTL (Register Transfer Level) simulations also referred as behavioral or pre-synthesis simulation — operates at a higher abstraction to verify design intent in terms of functionality — GLS runs on the synthesized netlist. This netlist contains the actual logic gates and interconnections mapped to the target technology.
- GLS ensures that the synthesized design behaves consistently with the RTL simulation results in terms of both logic and timing, now accounting for realistic timing delays and cell-level implementations. 
- It's a crucial step to confirm that synthesis hasn't introduced any functional mismatches or timing-related issues.
    


### 🔍 Why Pre-Synthesis and Post-Synthesis?
#### Pre-Synthesis Simulation:
- Focuses solely on verifying functionality based on the RTL code.
- Operates in a zero-delay environment; all events occur precisely on clock edges.
- Ideal for checking control/data logic correctness without concern for physical constraints.

#### Post-Synthesis Simulation (GLS - Gate-Level Simulation):
- Uses the synthesized gate-level netlist, incorporating technology-mapped standard cells.
- Captures functional behavior + timing delays, modeling more realistic hardware operation.
- Helps detect:
  - Timing issues like setup/hold violations.
  - Functional mismatches caused by synthesis artifacts (e.g., unintended latches).
  - Dynamic behaviors not visible through static analysis.
    
### Key Aspects of GLS for BabySoC:
1. **Verification with Timing Information:**
   - GLS is performed using Standard Delay Format (SDF) files to ensure timing correctness.
   - This checks if the SoC behaves as expected under real-world timing constraints.

2. **Design Validation Post-Synthesis:**
   - Confirms that the design's logical behavior remains correct after mapping it to the gate-level representation.
   - Ensures that the design is free from issues like metastability or glitches.

3. **Simulation Tools:**
   - Tools like Icarus Verilog or a similar simulator can be used for compiling and running the gate-level netlist.
   - Tool like yosys is used to generate the synthesised gate-level netlist
   - This netlist was then simulated using the same testbench as in RTL simulation. 
   - Waveforms were analyzed using GTKWave. The key goal was to compare waveform outputs from both stages and validate that functionality remains consistent. This validation step is essential to ensure that no logic is broken during synthesis and that the design is ready for physical implementation stages like place and route.

4. **Importance for BabySoC:**
   - BabySoC consists of multiple modules like the RISC-V processor, PLL, and DAC. GLS ensures that these modules interact correctly and meet the timing requirements in the synthesized design.


Step-by-Step execution plan for running the  commands manually:
---
## Prerequisite - for Synthesis using Yosys

The following cp commands copy **essential header files** from the **src/include directory** into the **working directory**. These include:
  - **sp_verilog.vh** – contains Verilog definitions and macros
  - **sandpiper.vh** – holds integration-related definitions for SandPiper
  - **sandpiper_gen.vh** – may include auto-generated or tool-generated parameters

```bash
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
cp -r src/include/sp_verilog.vh .
cp -r src/include/sandpiper.vh .
cp -r src/include/sandpiper_gen.vh .
```

```
ls
images  LICENSE  Makefile  output  README.md  sandpiper_gen.vh  sandpiper.vh  sp_env  sp_verilog.vh  src
```

![]()


#### **Step 1: Load the Top-Level Design and Supporting Modules**
- Launch the Yosys synthesis tool from your working directory.
  ```bash
  cd ~/VSD_RISCV_Kasturi/VSDBabySoc
  yosys
  ```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command1.png)


Inside the Yosys shell, 
Read the following in the Yosys environment
-  main vsdbabysoc.v RTL file
-  rvmyth.v with the include path using -I option.
-  clk_gate.v with the include path using -I option.
-
-  To Do So run:
```yosys

read_verilog /home/VSD_RISCV_Kasturi/VSDBabySoC/src/module/vsdbabysoc.v
read_verilog -I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/include /home/VSD_RISCV_Kasturi/src/module/rvmyth.v
read_verilog -I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/include /home/VSD_RISCV_Kasturi/src/module/clk_gate.v

```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command1.png)


### ❗Note:

_If you try to read the rvmyth.v file using yosys without copying the necessary header files first, you may encounter errors like:_

can't open include file "sp_verilog.vh'
can't open include file "sandpiper.vh'
can't open include file "sandpiper_gen.vh'

_To avoid these errors, make sure to copy the required include files into your working directory! This ensures Yosys can resolve them correctly during parsing, even if the -I option is used._
---

#### ✅ Step 2: Load the Liberty Files for Synthesis
Inside the same Yosys shell, run:
```yosys
yosys> read_liberty -lib /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/lib/avsdpll.lib 
yosys> read_liberty -lib /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/lib/avsddac.lib 
yosys> read_liberty -lib /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command2.png)

---

#### ✅ Step 3: Run Synthesis Targeting vsdbabysoc
```bash
yosys> synth -top vsdbabysoc
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command3.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command4.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command5.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command6.png)

---

#### ✅ Step 4: Map D Flip-Flops to Standard Cells
```yosys
yosys> dfflibmap -liberty /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command7.png)

---

#### ✅ Step 5: Perform Optimization and Technology Mapping

```bash
yosys> opt
yosys> abc -liberty /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command8.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command9.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command10.png)

| Step           | Purpose                                                              |
| -------------- | -------------------------------------------------------------------- |
| `strash`       | Structural hashing (reduces logic redundancy)                        |
| `scorr`        | Sequential sweeping for redundancy removal                           |
| `ifraig`       | Incremental FRAIGing (logic equivalence checking and optimization)   |
| `retime;{D}`   | Move registers across combinational logic to optimize timing         |
| `strash`       | Re-run structural hashing after retiming                             |
| `dch,-f`       | Delay-aware combinational optimization with fast mode                |
| `map,-M,1,{D}` | Map logic to gates minimizing area (`-M,1`) and retime-aware (`{D}`) |

---

#### ✅ Step 6: Perform Final Clean-Up and Renaming

```bash
yosys> flatten
yosys> setundef -zero
yosys> clean -purge
yosys> rename -enumerate
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command11.png)

| **Command**         | **Purpose / Usage**                                                                    |
| ------------------- | -------------------------------------------------------------------------------------- |
| `flatten`           | Flattens the entire design hierarchy into a single-level netlist.                      |
| `setundef -zero`    | Replaces all undefined (`x`) logic values with logical `0` to avoid simulation issues. |
| `clean -purge`      | Removes all unused wires, cells, and modules; `-purge` makes it more aggressive.       |
| `rename -enumerate` | Renames internal wires and cells to unique, numbered names for consistency.            |

---

#### ✅ Step 7: Check Statistics

```bash
yosys> stat
```
13. Printing statistics.

=== vsdbabysoc ===

![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command12.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command13.png)

#### ✅ Step 8: Write the Synthesized Netlist

```bash
yosys> write_verilog -noattr/home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/output/post_synth_sim/vsdbabysoc.synth.v
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command14.png)


---

### POST_SYNTHESIS SIMULATION AND WAVEFORMS
---
#### ✅ Step 1: Compile the Testbench
- To ensure that the synthesized Verilog file _(vsdbabysoc.synth.v)_ is available in the src/module directory for further processing or simulation, it is important to follow the below steps
- Before running the iverilog command, **copy** the necessary **standard cell** and **primitive models**:
- These files **must be present** in the same **directory** as the **testbench** (src/module) to resolve all module references during compilation.
  ```bash
  cd /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module
  cp -r ~/VSD_RISCV_Kasturi/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/sky130_fd_sc_hd.v .
  cp -r ~/VSD_RISCV_Kasturi/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/primitives.v .
  ```


Run the following `iverilog` command to compile the testbench:
```bash
cd ~/VSD_RISCV_Kasturi/VSDBabySoC/
iverilog -o /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/output/post_synth_sim/post_synth_sim.out -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 -I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/include -I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module/testbench.v
```


| **Option / Argument**                                                      | **Purpose / Description**                                                            |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `iverilog`                                                                 | Icarus Verilog compiler used to compile Verilog files into a simulation executable.  |
| `-o /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/output/post_synth_sim/post_synth_sim.out` | Specifies the output binary file for simulation.                                     |
| `-DPOST_SYNTH_SIM`                                                         | Defines the macro `POST_SYNTH_SIM` (used in testbench to switch simulation modes).   |
| `-DFUNCTIONAL`                                                             | Defines `FUNCTIONAL` to use behavioral models instead of detailed gate-level timing. |
| `-DUNIT_DELAY=#1`                                                          | Assigns a unit delay of `#1` to all gates for post-synthesis simulation.             |
| `-I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/include`                              | Adds the `include` directory to the search path for `\`include\` directives.         |
| `-I /home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module`                               | Adds the `module` directory to the include path for additional module references.    |
| `/home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module/testbench.v`                      | Specifies the testbench file as the top-level design for simulation.                 |

#### ❗Note - You may encounter these errors:
You may encounter following errors:

<strong> 🔴 Error 1:</strong>

```bash

/home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module/testbench.v:10: Include file vsdbabysoc.synth.v not found
No top level modules, and no -s option.
```
<strong> 🟢 To resolve this error copy the vsdbabysoc.synth.v from output/post_synth_sim directory to src/module directory</strong> 
  
<strong> 🔴 Error 2:</strong>

```bash
/home/pkasturi/VSD_RISCV_Kasturi/VSDBabySoC/src/module/sky130_fd_sc_hd.v:74452: syntax error
I give up.
```

<strong> 🟢 _To resolve this : Update the syntax in the file sky130_fd_sc_hd.v at or around line 74452._</strong>

###### Change:
```bash
`endif SKY130_FD_SC_HD__LPFLOW_BLEEDER_FUNCTIONAL_V
```

###### To:
```bash
`endif // SKY130_FD_SC_HD__LPFLOW_BLEEDER_FUNCTIONAL_V
```
---
#### ✅ Step 2: Navigate to the Post-Synthesis Simulation Output Directory
```bash
cd output/post_synth_sim/
```
#### ✅ Step 3: Run the Simulation
```bash
./post_synth_sim.out
```
---

#### ✅ Step 4: View the Waveforms in GTKWave
```bash
gtkwave post_synth_sim.vcd
```
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Command15.png)
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis01.png)
**IMAGE_17**
**IMAGE_18**
**IMAGE_18a**

#### To view output in analog mode
![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis02.png)

---


### Comparing Pre-Synthesis and Post-Synthesis Output
To ensure that the synthesis process did not alter the original design behavior, the output from the pre-synthesis simulation was compared with the post-synthesis simulation.

Both simulations were run using GTKWave, and the resulting waveforms were observed.

#### Pre-Synthesis Simulation Waveform

![]()

#### Post-Synthesis Simulation Waveform

![](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week3/Images/Post_Synthesis01.png)

<strong> Observation:</strong> ✅ _The outputs of post-synthesis simulation match exactly with pre-synthesis simulation waveforms, confirming that the functionality is preserved across the synthesis flow._

_This validates that the synthesized netlist is functionally equivalent to the RTL design._

