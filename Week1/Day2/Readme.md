# 🚀 Day 2: Timing Libraries, Synthesis Approaches, and Efficient Flip-Flop Coding
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Day-2-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to Day 2 of the RTL Workshop. This day covers three crucial topics:
- Understanding the `.lib` timing library (sky130_fd_sc_hd__tt_025C_1v80.lib) used in open-source PDKs.
- Comparing hierarchical vs. flat synthesis methods.
- Exploring efficient coding styles for flip-flops in RTL design.

---

# Contents

-  1.[Timing Libraries](#timing-libraries)
   - [SKY130 PDK Overview](#sky130-pdk-overview)
   - [Decoding sky130_fd_sc_hd__tt_025C_1v80.lib in SKY130 PDK](#decoding-sky130_fd_sc_hd__tt_025C_1v80.lib-in-sky130-pdk)
   - [Opening and Exploring the .lib File](#opening-and-exploring-the-lib-file)

- 2.[Hierarchical vs. Flattened Synthesis](#hierarchical-vs-flattened-synthesis)
  - [Hierarchical Synthesis](#hierarchical-synthesis)
  - [Flattened Synthesis](#flattened-synthesis)
  - [Key Differences](#key-differences)

- 3.[Flip-Flop Coding Styles](#flip-flop-coding-styles)
  - [Asynchronous Reset D Flip-Flop](#asynchronous-reset-d-flip-flop)
  - [Asynchronous Set D Flip-Flop](#asynchronous-set-d-flip-flop)
  - [Synchronous Reset D Flip-Flop](#synchronous-reset-d-flip-flop)

- 4.[Simulation and Synthesis Workflow](#simulation-and-synthesis-workflow)
  - [Icarus Verilog Simulation](#icarus-verilog-simulation)
  - [Synthesis with Yosys](#synthesis-with-yosys)

---

## Timing Libraries

### SKY130 PDK Overview

The SKY130 PDK is an open-source Process Design Kit based on SkyWater Technology's 130nm CMOS technology. It provides essential models and libraries for integrated circuit (IC) design, including timing, power, and process variation information.

### Decoding sky130_fd_sc_hd__tt_025C_1v80.lib in SKY130 PDK
- **sky130** : process node (130 nm)
- **fd** : foundry (SkyWater FD)
- **sc** : standard cell
- **hd** : high density variant
- **tt**: Typical process corner.
- **025C**: Represents a temperature of 25°C, relevant for temperature-dependent performance.
- **1v80**: Indicates a core voltage of 1.8V.

This naming convention clarifies which process, voltage, and temperature conditions the library models.

---

### Opening and Exploring the .lib File

To open the sky130_fd_sc_hd__tt_025C_1v80.lib file:

1. **Install a text editor:**
   ```shell
   sudo apt install gedit
   ```
2. **Open the file:**
   ```shell
   gedit sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
 ![Screenshot_2025-05-29_11-43-13](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week1/Day2/sky130pdlib.png)


---

## Hierarchical vs. Flattened Synthesis

### Hierarchical Synthesis

- **Definition**: Retains the module hierarchy as defined in RTL, synthesizing modules separately.
- **How it Works**: Tools like Yosys process each module independently, using commands such as `hierarchy` to analyze and set up the design structure.

**Advantages:**
- Faster synthesis time for large designs.
- Improved debugging and analysis due to maintained module boundaries.
- Modular approach, aiding integration with other tools.

**Disadvantages:**
- Cross-module optimizations are limited.
- Reporting can require additional configuration.

**Example:**
![Screenshot_2025-05-29_19-04-48]()


---

### Flattened Synthesis

- **Definition**: Merges all modules into a single flat netlist, eliminating hierarchy.
- **How it Works**: The `flatten` command in Yosys collapses the hierarchy, allowing whole-design optimizations.

**Advantages:**
- Enables aggressive, cross-module optimizations.
- Results in a unified netlist, sometimes simplifying downstream processes.

**Disadvantages:**
- Longer runtime for large designs.
- Loss of hierarchy complicates debugging and reporting.
- Can increase memory usage and netlist complexity.

**Example:**

![Screenshot_2025-05-29_19-20-47](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week1/Day2/flatmultiple.png)

> **Important:** Hierarchical synthesis maintains sub-modules in the design, while flattening produces a netlist from the ground up.

---

### Key Differences

| Aspect                | Hierarchical Synthesis             | Flattened Synthesis           |
|-----------------------|------------------------------------|------------------------------|
| Hierarchy             | Preserved                          | Collapsed                    |
| Optimization Scope    | Module-level only                  | Whole-design                 |
| Runtime               | Faster for large designs           | Slower for large designs     |
| Debugging             | Easier (traces to RTL)             | Harder                       |
| Output Complexity     | Modular structure                  | Single, complex netlist      |
| Use Case              | Modularity, analysis, reporting    | Maximum optimization         |

---

## Flip-Flop Coding Styles In Verilog

Flip-flops are fundamental sequential elements in digital design, used to store binary data. They form the basis of registers, counters, pipelines, and state machines. A flip-flop captures input (d) on a clock edge and holds its value until the next active edge.
Resets and sets are important to initialize or control flip-flops in designs. Below are different coding styles with explanations.

### Theory of Reset/Set
- **Asynchronous Reset/Set**
   - Overrides the clock.
   - Takes effect immediately when reset/set is asserted.
   - Useful for global initialization at power-up.
   - Risk: Can cause metastability if released close to a clock edge.

- **Synchronous Reset/Set**
   - Only active on the clock edge.
   - Ensures synchronous operation with other signals.
   - Safer in pipelined designs.
   - Slightly slower than async because it waits for a clock edge

## Verilog Coding Styles

### Asynchronous Reset D Flip-Flop

```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- **Asynchronous reset**: Overrides clock, setting q to 0 immediately.
- **Edge-triggered**: Captures d on rising clock edge if reset is low.

### Asynchronous Set D Flip-Flop

```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
- **Asynchronous set**: Overrides clock, setting q to 1 immediately.

### Synchronous Reset D Flip-Flop

```verilog
module dff_syncres (input clk, input async_reset, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
- **Synchronous reset**: Takes effect only on the clock edge.

---
### Comparison of Reset Styles

| Feature               | Asynchronous Reset                    | Synchronous Reset            |
|-----------------------|---------------------------------------|------------------------------|
| Activation            | Immediate, ignores clock              | Only at clock edge           |
| Speed                 | Fast initialization                   | Waits for clock              |
| Risk                  | Can cause metastability if not synced | Safer                        |
| Usage                 | Global chip reset at power-up         | Local pipelines, FSMs        |
| Area Impact           | Adds extra reset pin logic            | Usually smaller              |

---
## Simulation and Synthesis Workflow

### Icarus Verilog Simulation

1. **Compile:**
   ```shell
   iverilog dff_asyncres.v tb_dff_asyncres.v
   ```
2. **Run:**
   ```shell
   ./a.out
   ```
3. **View Waveform:**
   ```shell
   gtkwave tb_dff_asyncres.vcd
   ```
![Screenshot_2025-05-30_10-45-13]()


### Synthesis with Yosys

1. Start Yosys:
   ```shell
   yosys
   ```
2. Read Liberty library:
   ```shell
   read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
3. Read Verilog code:
   ```shell
   read_verilog /path/to/dff_asyncres.v
   ```
4. Synthesize:
   ```shell
   synth -top dff_asyncres
   ```
5. Map flip-flops:
   ```shell
   dfflibmap -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
6. Technology mapping:
   ```shell
   abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```
7. Visualize the gate-level netlist:
   ```shell
   show
   ```
![Screenshot_2025-05-30_11-03-00]()


---
## Summary
This overview provides you with practical insights into timing libraries, synthesis strategies, and reliable coding practices for flip-flops. Continue experimenting with these concepts to deepen your understanding of RTL design and synthesis.

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>
