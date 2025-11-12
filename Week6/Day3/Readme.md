# 🚀 Week 6 :  Sky130 Day 3 – Custom Inverter Layout
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Day 3** – **Custom Inverter Layout** we will go through SPICE simulation of a inverter cell whose SPICE deck is obtained from the layout of an inverter on the opensource tool `magic`. We open a custom inverter design in magic and extract the post-layout SPICE deck for SPICE simulation. The document also goes through simple DRC violations that are fixed by updating the `sky130A.tech` file. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [1️⃣ Floorplan Fundamentals](#1-floorplan-fundamentals)
   - [🗺️ What is Floorplanning?](#️-what-is-floorplanning)
   - [🎯 Floorplan Quality Metrics](#-floorplan-quality-metrics)
   - [✅ Good vs Bad Floorplan](#-good-vs-bad-floorplan)
   - [⚠️ Floorplan Red Flags](#️-floorplan-red-flags)
3. [2️⃣ Floorplan Configuration & Execution](#2-floorplan-configuration--execution)
   - [🎛️ Essential Floorplan Switches](#️-essential-floorplan-switches)
   - [📊 Configuration Guidelines](#-configuration-guidelines)
   - [🚀 Running Floorplan in OpenLANE](#-running-floorplan-in-openlane)
   - [📐 Area Calculation](#area-calculation)
4. [3️⃣ Analyzing and Visualizing Floorplan Results](#3-analyzing-and-visualizing-floorplan-results)
   - [📊 Floorplan Results Analysis](#-floorplan-results-analysis)
   - [🧩 I/O Placer Log Analysis](#-io-placer-log-analysis)
   - [📂 DEF File (Design Exchange Format)](#-def-file-design-exchange-format)
   - [📈 Key Metrics Extraction](#-key-metrics-extraction)
   - [🎨 Visualizing with MAGIC Layout Viewer](#-visualizing-with-magic-layout-viewer)
   - [🔍 Critical Areas to Inspect](#-critical-areas-to-inspect)
5. [4️⃣ Placement Commands](#placement-commands)
   - [📂 Loading the .def File into Magic](#loading-the-def-file-into-magic)
   - [✅ Quality Checks](#-quality-checks)
   - [⚠️ Common Issues and Fixes](#️-common-issues-and-fixes)
6. [💡 Key Takeaways](#-key-takeaways)
7. [📚 Repository & Author](#-repository--author)
   
---

## Objective

In this document we will go through SPICE simulation of a inverter cell whose SPICE deck is obtained from the layout of an inverter on the opensource tool `magic`. We open a custom inverter design in magic and extract the post-layout SPICE deck for SPICE simulation. The document also goes through simple DRC violations that are fixed by updating the `sky130A.tech` file. 

# Custom Inverter Layout
Inorder to open the custom inverter layout on magic, we must clone the github repo given below

```bash 
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
ls -ltr
```

![VSDLIB]()

Run the command below to open the inverter layout in magic 

```bash
magic -T sky130A.tech sky130_inv.mag &
```

![Inverter]()

## Identification of NMOS and PMOS

To indentify NMOS and PMOS in the layout, simply hover over the white box shown in the image and press 's'. On the `tkcon 2.3 Main` file you can see it's description

![NMOS]()
![PMOS]()

## Indentifying Correct Connections
In order to check the proper connectivity of the drain terminals of the transistor with the output port, the source terminal of PMOS with VDD and source terminal of NMOS with GND, simply hover over the terminals and press 's' two times and you will get the desired output as shown 

- Output Port Connectivity
![output]()

- PMOS Source with VDD
![PMOS SRC]()

- NMOS Source with GND
![NMOS SRC]()

## Deleting Parts to View DRC
In magic, with a predefined layout, we can delete parts to check the DRC violations as shown below:

![DRC Violation]()

## SPICE Extraction from Layout

We can extract the SPICE deck/netlist from the layout by following these commands. Make sure to type them in the `tkon 2.3 Main` window.

```bash
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

- SPICE Extraction in tkon
![SPICE Extraction]()

The .spice file obtained is stored in the directory as shown below

![Spice directory]()

The obtained netlist is shown below

![Netlist]()

## SPICE Simulation
Inorder to run the SPICE simulation, we update the obtained inverter netlist, and add:
- Update the scale option according to the size of a `root box` which is `0.01u`
- Include library files `pshort.lib` and `nshort.lib`
- Add `DC supply` voltage of 3.3V and `Pulsating supply` of 3.3V
- Update the model names of trasistors to `pshort_model.0` and `nshort_model.0`
- Capacitance - `C6` to smoothen out sharp edges. The update netlist is shown below:
- Add command to perform `transient analysis`.
![Root Box]()
![Updated netlist]()

After saving the file, we go back to the terminal and perform SPICE simulation in `ngspice`. Followi these commands:

```bash
ngspice sky130A_inv.spice # enters into ngspice environment
plot y vs time a # Performs transient analysis
```
![ngspice]()

Upon running the command, we obtain the following result:
![transient analysis]()

## Characterization of Inverter
From the transient analysis output, we can characterize the cell by finding it's:
- Rise Transistion Time
- Fall Transistion Time
- Rise Delay 
- Fall Delay 

Let's look at each of them one by one.

### Rise Transition Time
It is defined as the difference between output rise time at 80% voltage and 20% voltage. 
- 80% Vin = 2.64V   Time at 2.64V = 2.24669 ns
- 20% Vin = 0.66V   Time at 0.66V = 2.18234 ns

Therefore, Rise Transistion Time = 2.24669 - 2.18234 = 0.28456 ns

- Rise Transition at 20%
![Rise 20%]()

- Rise Transition at 80%
![Rise 80%]()

### Fall Transision Time
It is defined as the difference between output rise time at 80% voltage and 20% voltage. 
- 80% Vin = 2.64V   Time at 2.64V = 4.05318 ns
- 20% Vin = 0.66V   Time at 0.66V = 4.09554 ns

Therefore, Fall Transistion Time = 4.09554 - 4.05318 = 0.04236 ns

- Fall Transition at 80%
![Fall 80%]()

- Fall Transistion at 20%
![Fall 20%]()

### Rise Propogation Delay 
It is defined as the the difference in timeing values at 50% voltage between output and input during the positive edge. At 50% Vin which is equal to 1.65V 
- Input Signal Time at 1.65V = 2.14989 ns
- Outpu Signal Time at 1.65V = 2.21121 ns

Therefore, the Rise Propogation Delay = 2.21121 - 2.14989 = 0.06132 ns

- Delay at 50% Vin
![Rise Prop]()

### Fall Propogation Delay
It is defined as the the difference in timeing values at 50% voltage between input and output during the negative edge. At 50% Vin which is equal to 1.65V 
- Input Signal Time at 1.65V = 4.0779 ns
- Outpu Signal Time at 1.65V = 4.04986 ns

Therefore, the Rise Propogation Delay = 4.0779 - 4.04986 = 0.02804 ns

The values obtained for the calculations are shown below
![Calc Values]()

# Magic Examples
This section of tasks describes how to install the examples of layouts to check for DRC violations along with changed made to correct the DRC violations. 

To get a good understanding of the Magic tool follow this link
[link]()


Command to install the examples is as follows:

```bash
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
ls -al
gvim .magicrc
magic -d XR &
```

- Installing the tar-ball package
![Installing the package]()

- Magicrc file 
![Magic rc file]()

- List of examples downloaded
![Examples]()

- Command to open Magic tool
![Command]()

## Incorrectly Implemented poly.9

- Poly Rules 
![Poly Rules]()

For this we load the `poly.mag` file as shown below
![Loding poly.mag]()
![Polymag file]()

The DRC violation for the zoomed in images is shown below
![Violation]()

### Lines Added in sky130A.tech file
Inorder for these viloations to be fixed we add/update a few lines inside the sky130A.tech file.
- Line updation 1
![Line update 1]()

- Line Updation 2
![Line Update 2]()

Upon saving the `sky130A.tech` file, we can simply use the command given below so that it can be used in the existing magic session in the `tkon` window. 

```bash
tech load sky130A.tech
drc check
drc why
```

### Additional Assignment
![Additional Assignment fail]()
![Additional Assignment pass]()

# Incorrectly Implemented nwell.4 rules
- nwell rules
![nwell]()

No DRC violation even though no tap present 
![drc vio1]()

We update the `sky130A.tech` file to fix the DRC violation

- Updating sky130A.tech file
![Update sky130A.tech file]()
![Update sky130A.tech file]()
![Update sky130A.tech file]()

To include updated .tech file use the commads given below in the tkon window

```bash
tech load sky130A.tech
drc check
drc why
```
- Output of DRC why
![Nwell DRC]()

- Placing TAP and checking DRC
![Nwell tap DRC]()

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---

