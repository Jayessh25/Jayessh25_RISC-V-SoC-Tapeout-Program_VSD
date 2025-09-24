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

<div align="center">
  <img src="https://github.com/user-attachments/assets/93927b96-df80-4da5-b801-284fc2cc6757" alt="Design & Testbench Overview" width="70%">
</div>

---

## 2. Getting Started with iverilog

**iverilog** is an open-source Verilog simulator. Typical workflow:

<div align="center">
  <img src="https://github.com/user-attachments/assets/3ca190fb-cfa4-4abb-b9e1-0151b3c4bdba" alt="iverilog Simulation Flow" width="70%">
</div>

- Provide design and testbench as input.  
- Generates `.vcd` waveform file for GTKWave.

---

## 3. Lab: 2-to-1 Multiplexer Simulation

### Step 1: Clone Repository
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files

