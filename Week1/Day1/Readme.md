# 🚀 Day 1: Verilog RTL Design & Synthesis Workshop

<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Day-1-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Welcome to **Day 1** of the RTL Workshop!  
Today, you'll dive into **Verilog RTL design**, **open-source simulation with Icarus Verilog (iverilog)**, and **logic synthesis using Yosys**. This guide walks through labs, essential concepts, and practical exercises to build a strong RTL foundation.

---

## 📋 Table of Contents

1. [Simulator, Design, and Testbench](#1-simulator-design-and-testbench)  
2. [Getting Started with iverilog](#2-getting-started-with-iverilog)  
3. [Lab: 2-to-1 Multiplexer Simulation](#3-lab-2-to-1-multiplexer-simulation)  
4. [Verilog Code Analysis](#4-verilog-code-analysis)  
   - [Design: `good_mux.v`](#design-good_muxv)  
   - [Testbench: `tb_good_mux.v`](#testbench-tb_good_muxv)  
5. [Introduction to Yosys & Gate Libraries](#5-introduction-to-yosys--gate-libraries)  
6. [Synthesis Lab with Yosys](#6-synthesis-lab-with-yosys)  
7. [Summary](#7-summary)  

---

## 1. Simulator, Design, and Testbench

### Simulator
A **simulator** checks your digital circuit’s functionality by applying test inputs and observing outputs, helping verify designs before hardware implementation.

### Design
The **design** is your Verilog code describing the intended logic.

### Testbench
A **testbench** applies inputs to your design and checks outputs for correctness.

---

## 2. Getting Started with iverilog

**iverilog** is an open-source Verilog simulator. Typical workflow:

- Provide design and testbench as input.  
- Generates `.vcd` waveform file for GTKWave.

---

## 3. Lab: Simulating a 2-to-1 Multiplexer

Let’s simulate a simple **2-to-1 multiplexer** using iverilog!

###  Step 1: Clone the Workshop Repository

```shell
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

###  Step 2: Install Required Tools

```shell
sudo apt install iverilog
sudo apt install gtkwave
```

###  Step 3: Simulate the Design

Compile the design and testbench:

```shell
iverilog good_mux.v tb_good_mux.v
```

Run the simulation:

```shell
./a.out
```

View the waveform:

```shell
gtkwave tb_good_mux.vcd
```

<div align="center">
  <img src="" alt="GTKWave Example" width="70%">
</div>

---

## 4. Verilog Code Analysis

**The code for the multiplexer (`good_mux.v`):**

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

**The Testbench code for the multiplexer (`tb_good_mux.v`):**

```verilog
module tb_good_mux;
	reg i0,i1,sel;
	wire y;
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);
	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end
always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```
###  **How It Works**

- **Inputs:** `i0`, `i1` (data), `sel` (select line)
- **Output:** `y` (registered output)
- **Logic:** If `sel` is 1, `y` gets `i1`; if `sel` is 0, `y` gets `i0`.

---

## 5. Introduction to Yosys & Gate Libraries

###  What is Yosys?

**Yosys** is a powerful open-source synthesis tool for digital hardware. It takes your Verilog code and converts it into a gate-level netlist—a hardware blueprint.

#### Yosys Features

- **Synthesis:** Converts HDL to a logic circuit
- **Optimization:** Improves speed or area
- **Technology Mapping:** Matches logic to actual hardware cells
- **Verification:** Checks correctness
- **Extensibility:** Supports custom flows

###  Why Do Libraries Have Different Gate "Flavors"?

A `.lib` file contains many versions of each gate (like AND, OR, NOT) with different properties:

- **Performance:** Faster gates for critical paths, slower for power savings
- **Power:** Some gates use less energy
- **Area:** Smaller gates for compact chips
- **Drive Strength:** Stronger gates to drive more load
- **Signal Integrity:** Specialized gates for noise/performance
- **Mapping:** Synthesis tools pick the best flavor for your needs

---

## 6. Synthesis Lab with Yosys

Let’s synthesize the `good_mux` design using Yosys!

###  Step-by-Step Yosys Flow

1. **Start Yosys**
    ```shell
    yosys
    ```

2. **Read the liberty library**
    ```shell
    read_liberty -lib /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

3. **Read the Verilog code**
    ```shell
    read_verilog /home/vsduser/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files/good_mux.v
    ```

4. **Synthesize the design**
    ```shell
    synth -top good_mux
    ```

5. **Technology mapping**
    ```shell
    abc -liberty /address/to/your/sky130/file/sky130_fd_sc_hd__tt_025C_1v80.lib
    ```

6. **Visualize the gate-level netlist**
    ```shell
    show
    ```

<div align="center">
  <img src="" alt="Yosys Gate-level Schematic" width="70%">
</div>

---

## 7. Summary

- You learned about simulators, designs, and testbenches.
- You ran your first Verilog simulation with iverilog and visualized waveforms.
- You analyzed the 2-to-1 mux code.
- You explored Yosys and learned why gate libraries have various flavors.


---

