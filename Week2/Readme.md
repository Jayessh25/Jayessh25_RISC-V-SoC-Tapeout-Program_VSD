
# 🚀 Day 6: BabySoC Fundamentals & Functional Modelling
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Day-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to Day 6 of the RTL workshop!Today, we dive into System-on-Chip (SoC) fundamentals and learn how to model the BabySoC functionally. Using Icarus Verilog for simulation and GTKWave for waveform analysis, we understand how processor, memory, clock, and peripherals integrate in a real SoC.

---
## 📑 Table of Contents

- [1. What is a System-on-Chip (SoC)?](#1-what-is-a-system-on-chip-soc)
- [2. Types of SoCs](#2-types-of-socs)
- [3. Components of a Typical SoC](#3-components-of-a-typical-soc)
- [4. Introduction to VSDBabySoC](#4-introduction-to-vsdbabysoc)
- [5. What is VSDBabySoC?](#5-what-is-vsdbabysoc)
- [6. Problem Statement](#6-problem-statement)
- [7. What is SoC?](#7-what-is-soc)
- [8. What is RVMYTH?](#8-what-is-rvmyth)
- [9. What is PLL?](#9-what-is-pll)
- [10. What is DAC?](#10-what-is-dac)
- [11. Project Structure](#11-project-structure)
- [12. TLV to Verilog Conversion for RVMYTH](#12-tlv-to-verilog-conversion-for-rvmyth)
- [13. Simulation Steps](#13-simulation-steps)
  - [13.1 Pre-Synthesis Simulation](#131-pre-synthesis-simulation)
  - [13.2 Viewing Waveform in GTKWave](#132-viewing-waveform-in-gtkwave)
  - [13.3 Viewing DAC Output in Analog Mode](#133-viewing-dac-output-in-analog-mode)
  - [13.4 Trouble Shooting Tips](#134-trouble-shooting-tips)
- [14. Why Pre-Synthesis and Post-Synthesis?](#14-why-pre-synthesis-and-post-synthesis)
- [15. Summary](#15-summary)

---

## 1. What is a System-on-Chip (SoC)?

A System-on-Chip (SoC) integrates all essential components of a computer system on a single chip, reducing size, power, and cost while improving performance.
- Earlier: CPU, memory, and peripherals were spread across multiple chips.
- Now: Processor, memory, interconnect, and peripherals all inside one chip.
- Found in smartphones, IoT, wearables, automotive controllers, and AI accelerators.

---

## 2. Types of SoCs
- Application-specific SoCs → optimized for mobile, automotive, or camera systems.
- General-purpose SoCs → found in PCs and dev boards (e.g., Raspberry Pi).
- Heterogeneous SoCs → mix CPUs, GPUs, DSPs, accelerators.
- Open-source SoCs → based on RISC-V and other open ISAs.
👉 BabySoC belongs to the open-source educational SoC category.

---

## 3. Components of a Typical SoC

- CPU (Processor Core) → the brain; executes instructions.
    - BabySoC uses RVMYTH (RISC-V core).
- Memory → stores instructions and data.
- Peripherals (I/O) → UART, SPI, GPIO, DAC, etc.
    - BabySoC uses a DAC for digital-to-analog conversion.
- Interconnect / Bus System → communication backbone.
    - BabySoC uses a simplified bus for education.
- Clock & Power → synchronization and efficiency.
    - BabySoC includes a PLL for stable clock generation.

---
## 4. Introduction to VSDBabySoC

The VSDBabySoC is an open-source, simplified System-on-Chip (SoC) built for educational purposes under the VLSI System Design (VSD) program.
It integrates a RISC-V CPU core (RVMYTH), a PLL, and a DAC, showcasing how digital and analog blocks interact in a real chip.

---
## 5. What is VSDBabySoC?
VSDBabySoC is a compact, open-source System-on-Chip (SoC) designed as part of the VLSI System Design (VSD) program.
It is based on RVMYTH – a simple RISC-V processor core designed in TL-Verilog.
The SoC integrates:
- RVMYTH Core (CPU) → executes RISC-V instructions.
- PLL (Phase Locked Loop) → generates a stable, high-frequency clock signal from an input clock.
- 10-bit DAC (Digital-to-Analog Converter) → converts the digital output of the processor into an analog signal (e.g., for audio or video)
- A miniature SoC designed using open-source IPs.
- Built on Sky130 PDK (open-source process design kit).
- Demonstrates integration of processor, clock management, and peripheral.
- Tape-out ready and suitable for beginner-level functional modelling → RTL → physical design flow.

---
## ⚙️ How Does VSDBabySoC Work?

Let’s break down its functional flow:
## 1.Input (Clock & Instructions)
- The system receives an input clock.
- The PLL multiplies/divides this clock to provide the correct frequency for the CPU.
- The CPU (RVMYTH) fetches instructions from memory (usually a small program).
## 2.Processing (RVMYTH Core)
- The RISC-V core executes instructions like arithmetic, logic, memory access, etc.
- These instructions generate digital signals representing processed data.
## 3.Digital-to-Analog Conversion (DAC)
- The digital output is sent to the DAC.
- The DAC converts the binary digital values into a corresponding analog voltage.
- Example:
        - Digital 1111111111 (10-bit max) → max analog voltage.
        - Digital 0000000000 → 0V.
        - Intermediate values → proportional voltages.
## 4. Output (Analog Signal)
   - The analog signal can drive external devices like speakers (audio) or displays (video).
   - This allows BabySoC to communicate with the real world.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbabysoc_block_diagram.png)

---
🔑 Why is BabySoC Important?

- It demonstrates complete SoC integration in a simplified way.
- Shows digital + analog interaction (CPU + DAC).
- Uses open-source tools and Sky130 PDK, making it reproducible by students/researchers.
- Provides a stepping stone for beginners to understand how real SoCs (like those in phones/laptops) are built.

✅ So, VSDBabySoC = RISC-V CPU (RVMYTH) + PLL + DAC = Digital + Analog Mini-SoC for Learning.

---

## 6. Problem Statement

Modern SoCs integrate many subsystems. Beginners often find it difficult to learn integration because:
- Full-scale SoCs are too complex.
- Proprietary IPs are not open-source.
- Lack of hands-on, tape-out-ready projects.
**👉 Solution:** VSDBabySoC provides a minimal, open, and practical SoC for learning and experimentation.

---

## 7. What is SoC?

A System-on-Chip (SoC) is a single chip that integrates:
- CPU (Processor Core)
- Memory (SRAM, DRAM, Flash, cache)
- Peripherals (UART, SPI, GPIO, ADC/DAC)
- Interconnect/Buses (AXI, AHB, APB)
- Clock & Power Management

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/3.png)

**👉 Benefits:**

- Compact, power-efficient, and high-performance.
- Found in smartphones, IoT, wearables, and AI accelerators.

---
## 8. What is RVMYTH?

RVMYTH = a RISC-V based CPU core used in BabySoC.
Designed during the MYTH (Microprocessor for You in Thirty Hours) workshop.

**Features:**
- Simple RISC-V instruction set.
- Executes a small program producing a 10-bit digital output.
- Represents the brain of the BabySoC.

---
## 9. What is PLL?

- PLL (Phase Locked Loop) is a clock-generation circuit.
- Provides a stable, high-frequency clock for the CPU.
- Ensures synchronization across the SoC.

**In BabySoC:**

- Drives the RVMYTH core with a clean and stable clock signal.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/2.png)

---
## 10. What is DAC?

- DAC (Digital-to-Analog Converter) converts digital signals → analog outputs.
- Useful in audio, video, and sensor interfacing.

**In BabySoC:**

-  A 10-bit DAC converts CPU digital output into analog form.
- Demonstrates mixed-signal integration inside SoCs.

![VSDBabySoc Block Diagram](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/1.png)

---

## 11. Project Structure
- src/include/ - Contains header files (*.vh) with necessary macros or parameter definitions.
- src/module/ - Contains Verilog files for each module in the SoC design.
- output/ - Directory where compiled outputs and simulation files will be generated.

### Setup and Prepare Project Directory
Clone or set up the directory structure as follows:
```txt
VSDBabySoC/
├── src/
│   ├── include/
│   │   ├── sandpiper.vh
│   │   └── other header files...
│   ├── module/
│   │   ├── vsdbabysoc.v      # Top-level module integrating all components
│   │   ├── rvmyth.v          # RISC-V core module
│   │   ├── avsdpll.v         # PLL module
│   │   ├── avsddac.v         # DAC module
│   │   └── testbench.v       # Testbench for simulation
└── output/
└── compiled_tlv/         # Holds compiled intermediate files if needed
```
---
### 🛠️ Cloning the Project

To begin, clone the VSDBabySoC repository using the following command:

```bash
step 1: cd ~/VLSI

step 2: git clone https://github.com/manili/VSDBabySoC.git

step 3: cd ~/VLSI/VSDBabySoC/

step 4: jayesshsk@jayesshsk-VirtualBox:~/VLSI$ ls VSDBabySoC/
images  LICENSE  Makefile  README.md  src

step 5: jayesshsk@jayesshsk-VirtualBox:~/VLSI$ ls VSDBabySoC/src/module/
avsddac.v  avsdpll.v  clk_gate.v  pseudo_rand_gen.sv  pseudo_rand.sv  rvmyth_gen.v  rvmyth.tlv  rvmyth.v  testbench.rvmyth.post-routing.v  testbench.v  vsdbabysoc.v
```
---

### 12. TLV to Verilog Conversion for RVMYTH

Initially, you will see only the `rvmyth.tlv` file inside `src/module/`, since the RVMYTH core is written in TL-Verilog.
To convert it into a `.v` file for simulation, follow the steps below:

<strong>🔧 TLV to Verilog Conversion Steps</strong>

```bash
# Step 1: Install python3-venv (if not already installed)
sudo apt update
sudo apt install python3-venv python3-pip

# Step 2: Create and activate a virtual environment
cd ~/VLSI/VSDBabySoC/
python3 -m venv sp_env
source sp_env/bin/activate

# Step 3: Install SandPiper-SaaS inside the virtual environment
pip install pyyaml click sandpiper-saas

# Step 4: Convert rvmyth.tlv to Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```
 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/Succesful%20creation%20of%20.v%20file.png)

✅ After running the above command, rvmyth.v will be generated in the src/module/ directory.

You can confirm this by listing the files:

```bash
jayesshsk@jayesshsk-VirtualBox:~$ cd VLSI/VSDBabySoC/
jayesshsk@jayesshsk-VirtualBox:~/VLSI/VSDBabySoC$ ls src/module/
avsddac.v  avsdpll.v  clk_gate.v  pseudo_rand_gen.sv  pseudo_rand.sv  rvmyth_gen.v  rvmyth.tlv  rvmyth.v  testbench.rvmyth.post-routing.v  testbench.v  vsdbabysoc.v
```

#### Note 
To use this environment in future sessions, always activate it first:
```bash
jayesshsk@jayesshsk-VirtualBox:~$ source sp_env/bin/activate
```
To exit:
```bash
jayesshsk@jayesshsk-VirtualBox:~$ deactivate
```

### 13. Simulation Steps

#### 13.1 Pre-Synthesis Simulation

Run the following command to perform a pre-synthesis simulation:

```bash
step 1: cd ~/VLSI/VSDBabySoC/

step 2: mkdir -p output/pre_synth_sim

step 3: iverilog -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM -I src/include -I src/module src/module/testbench.v
```

Then run:
```bash
step 4: cd output/pre_synth_sim

step 5: ./pre_synth_sim.out
```

Explanation:

- DPRE_SYNTH_SIM: Defines the PRE_SYNTH_SIM macro for conditional compilation in the testbench.
- The resulting pre_synth_sim.vcd file can be viewed in GTKWave.

#### 13.2 Viewing Waveform in GTKWave

After running the simulation, open the VCD file in GTKWave: 

```bash
step 6: gtkwave pre_synth_sim.vcd
```
Drag and drop the CLK, reset, OUT (DAC), and RV TO DAC [9:0] signals to their respective locations in the simulation tool

 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/VSD%20gtkwave1.png)

**👉 Conclusion (Waveform 1):**
This view confirms that the DAC is correctly mapping digital values to proportional analog voltages step by step
In this picture we can see the following signals:

**CLK**: This is the input CLK signal of the RVMYTH core. This signal comes from the PLL, originally.

**reset**: This is the input reset signal of the RVMYTH core. This signal comes from an external source, originally.

**OUT**: This is the output OUT signal of the VSDBabySoC module. This signal comes from the DAC (due to simulation restrictions it behaves like a digital signal which is incorrect), originally.

**RV_TO_DAC[9:0]**: This is the 10-bit output [9:0] OUT port of the RVMYTH core. This port comes from the RVMYTH register #17, originally.

**OUT**: This is a real datatype wire which can simulate analog values. It is the output wire real OUT signal of the DAC module. This signal comes from the DAC, originally. 

This can be viewed by changing the Data Format of the signal to Analog → Step

#### 13.3 Viewing DAC output in analog mode 

Drag and drop the CLK, reset, OUT (DAC) (as analog step), and RV TO DAC [9:0] signals to their respective locations in the simulation tool 
 ![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbaby%20gtkwave%202.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week2/vsdbaby%20gtkwave%203.png)

**👉 Conclusion (Waveform 2):**
This zoomed-out view shows the DAC producing a waveform-like analog signal (instead of individual digital steps). This represents the real-world analog signal you’d get if you connected BabySoC’s DAC output to a speaker or display.

### 13.4 Trouble shooting tips

   - Module Redefinition: If you encounter redefinition errors, ensure modules are included only once, either in the testbench or in the command line.
   - Path Issues: Verify paths specified with -I are correct. Use full paths if relative paths cause errors.
---
## 14. Why Pre-Synthesis and Post-Synthesis?

1. **Pre-Synthesis Simulation**: 
   - Focuses only on verifying functionality based on the RTL code.
   - Zero-delay environment, with events occurring on the active clock edge.

2. **Post-Synthesis Simulation (GLS)**:
   - Uses the synthesized netlist (gate-level) to simulate both functionality and timing.
   - Identifies timing violations and potential mismatches (e.g., unintended latches).
   - Helps verify dynamic circuit behavior that static methods may miss.

---

## Summary

- BabySoC = RVMYTH (CPU) + PLL (Clock) + DAC (Analog output)
- SoC = CPU + memory + peripherals + interconnect on one chip
- Pre-synthesis sim → checks RTL functionality
- Post-synthesis sim (GLS) → checks timing + functionality
- Use complete if-else/case to avoid latches
- For/Generate loops → scalable Verilog coding
- Tools: Icarus Verilog + GTKWave
  
---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
