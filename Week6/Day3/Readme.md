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
2. [⚙️ Custom Inverter Layout Setup](#️-custom-inverter-layout-setup)
3. [🔍 Identification of NMOS and PMOS](#-identification-of-nmos-and-pmos)
4. [📏 Identifying Correct Connections](#-identifying-correct-connections)
5. [⚠️ Deleting Parts to View DRC](#️-deleting-parts-to-view-drc)
6. [🧩 SPICE Extraction from Layout](#-spice-extraction-from-layout)
7. [⚡ SPICE Simulation](#-spice-simulation)
8. [📊 Characterization of Inverter](#-characterization-of-inverter)
   - [🔼 Rise Transition Time](#-rise-transition-time)
   - [🔽 Fall Transition Time](#-fall-transision-time)
   - [📈 Rise Propagation Delay](#-rise-propogation-delay)
   - [📉 Fall Propagation Delay](#-fall-propogation-delay)
9. [🏗️ Magic Examples and DRC Checks](#️-magic-examples)
10. [📐 Incorrectly Implemented poly.9 Rule Fix](#-incorrectly-implemented-poly9)
11. [🧱 Incorrectly Implemented nwell.4 Rule Fix](#-incorrectly-implemented-nwell4)
12. [💡 Summary](#-summary)
13. [📚 Repository & Author](#-repository--author)

---

## Objective

The objective of **Day 3 – Custom Inverter Layout** is to design, extract, and simulate a **custom CMOS inverter** using open-source VLSI design tools.  
The process involves opening the inverter layout in **Magic**, extracting the **post-layout SPICE netlist**, and performing a **transient analysis** using **ngspice** to validate its switching characteristics.  
Additionally, this session demonstrates **identification of transistors and connections**, **fixing DRC violations**, and **updating the `sky130A.tech` file** to correctly implement rules such as `poly.9` and `nwell.4`.  
Through this flow, we bridge the gap between **layout-level design** and **circuit-level verification**, reinforcing the fundamentals of custom cell design.

---

# Custom Inverter Layout
Inorder to open the custom inverter layout on magic, we must clone the github repo given below

```bash 
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
ls -ltr
```

![VSDLIB](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment3.1(gitclone).png)

Run the command below to open the inverter layout in magic 

```bash
magic -T sky130A.tech sky130_inv.mag &
```

![Inverter](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment3.3(gitclone).png)
![Inverter](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment3.4.png)

## Identification of NMOS and PMOS

To indentify NMOS and PMOS in the layout, simply hover over the white box shown in the image and press 's'. On the `tkcon 2.3 Main` file you can see it's description

![NMOS](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174756.png)
![PMOS](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174832.png)

## Indentifying Correct Connections
In order to check the proper connectivity of the drain terminals of the transistor with the output port, the source terminal of PMOS with VDD and source terminal of NMOS with GND, simply hover over the terminals and press 's' two times and you will get the desired output as shown 

- Output Port Connectivity
![output](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174854.png)

- PMOS Source with VDD
![PMOS SRC](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174911.png)

- NMOS Source with GND
![NMOS SRC](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174926.png)

## Deleting Parts to View DRC
In magic, with a predefined layout, we can delete parts to check the DRC violations as shown below:

![DRC Violation](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20174950.png)

## SPICE Extraction from Layout

We can extract the SPICE deck/netlist from the layout by following these commands. Make sure to type them in the `tkon 2.3 Main` window.

```bash
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

- SPICE Extraction in tkon
![SPICE Extraction](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment4.3.png)

The .spice file obtained is stored in the directory as shown below

![Spice directory](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment4.4.png)

The obtained netlist is shown below

![Netlist](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment4.5.png)

## SPICE Simulation
Inorder to run the SPICE simulation, we update the obtained inverter netlist, and add:
- Update the scale option according to the size of a `root box` which is `0.01u`
- Include library files `pshort.lib` and `nshort.lib`
- Add `DC supply` voltage of 3.3V and `Pulsating supply` of 3.3V
- Update the model names of trasistors to `pshort_model.0` and `nshort_model.0`
- Capacitance - `C6` to smoothen out sharp edges. The update netlist is shown below:
- Add command to perform `transient analysis`.

![Updated netlist](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment4.7.png)

After saving the file, we go back to the terminal and perform SPICE simulation in `ngspice`. Followi these commands:

```bash
ngspice sky130A_inv.spice # enters into ngspice environment
plot y vs time a # Performs transient analysis
```
![ngspice](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.0.png)

Upon running the command, we obtain the following result:
![transient analysis](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.1.png)

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
- Rise Transition at 80%

### Fall Transision Time
It is defined as the difference between output rise time at 80% voltage and 20% voltage. 
- 80% Vin = 2.64V   Time at 2.64V = 4.05318 ns
- 20% Vin = 0.66V   Time at 0.66V = 4.09554 ns

Therefore, Fall Transistion Time = 4.09554 - 4.05318 = 0.04236 ns

### Rise Propogation Delay 
It is defined as the the difference in timeing values at 50% voltage between output and input during the positive edge. At 50% Vin which is equal to 1.65V 
- Input Signal Time at 1.65V = 2.14989 ns
- Outpu Signal Time at 1.65V = 2.21121 ns

Therefore, the Rise Propogation Delay = 2.21121 - 2.14989 = 0.06132 ns

### Fall Propogation Delay
It is defined as the the difference in timeing values at 50% voltage between input and output during the negative edge. At 50% Vin which is equal to 1.65V 
- Input Signal Time at 1.65V = 4.0779 ns
- Outpu Signal Time at 1.65V = 4.04986 ns

Therefore, the Rise Propogation Delay = 4.0779 - 4.04986 = 0.02804 ns
![Installing the package](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20175722.png)


# Magic Examples
This section of tasks describes how to install the examples of layouts to check for DRC violations along with changed made to correct the DRC violations. 


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
![Installing the package](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.4.png)
![Installing the package](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.5.png)

- Magicrc file 
![Magic rc file](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.6.png)


## Incorrectly Implemented poly.9

- Poly Rules 
![Poly Rules](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-10-31%20173051.png)

For this we load the `poly.mag` file as shown below
![Loding poly.mag](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.9.png)
![Polymag file](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment5.8opening%20poly.png)

The DRC violation for the zoomed in images is shown below
![Violation](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-11-12%20181217.png)

### Lines Added in sky130A.tech file
Inorder for these viloations to be fixed we add/update a few lines inside the sky130A.tech file.
- Line updation 1
![Line update 1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.1.png)

- Line Updation 2
![Line Update 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.2.png)

Upon saving the `sky130A.tech` file, we can simply use the command given below so that it can be used in the existing magic session in the `tkon` window. 

```bash
tech load sky130A.tech
drc check
drc why
```

### Additional Assignment
![Additional Assignment fail](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.3.png)
![Additional Assignment pass](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.8.png)

# Incorrectly Implemented nwell.4 rules
- nwell rules
![nwell](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Screenshot%202025-10-31%20180016.png)

No DRC violation even though no tap present 
![drc vio1](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.5nwellerror.png)

We update the `sky130A.tech` file to fix the DRC violation

- Updating sky130A.tech file
![Update sky130A.tech file](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.6.png)
![Update sky130A.tech file](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.7.png)


To include updated .tech file use the commads given below in the tkon window

```bash
tech load sky130A.tech
drc check
drc why
```
- Output of DRC why
![Nwell DRC](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.8.png)

- Placing TAP and checking DRC
![Nwell tap DRC](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day3/Images/Comment6.9.png)

---

## 💡 Summary

In this session, we successfully explored the **custom inverter layout design flow** using **Magic** and **Sky130 PDK**.  
We cloned and opened the inverter layout, extracted its **SPICE netlist**, and performed **transient analysis** using **ngspice** to characterize its **rise/fall transition and propagation delays**.  
Furthermore, we analyzed **DRC violations** such as `poly.9` and `nwell.4`, corrected them by updating the **sky130A.tech** file, and verified fixes using Magic’s DRC tools.  
This exercise demonstrated the complete process of **custom standard cell design**, from layout editing to post-layout simulation — forming a crucial foundation for **library characterization** and **ASIC physical design**.

---


<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---

