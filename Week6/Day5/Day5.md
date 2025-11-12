# 🚀 Week 6 :  Sky130 Day 5 – Power Distribution Network & Routing
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-6-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Day 5** –  generating a robust **Power Distribution Network (PDN)** to ensure stable power delivery across all standard cells and performing **detailed routing** to connect all logic nets.  The session focuses on executing OpenLANE commands for PDN creation, running **global and detailed routing**, and visualizing results using **Magic Layout**.  It also includes **post-route timing analysis** using **OpenROAD** to verify design timing closure, ensuring that setup and hold constraints are satisfied after parasitic extraction.  

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [⚙️ Power Distribution Network (PDN)](#-power-distribution-network)
   - [🔧 Commands to Generate PDN](#-commands-to-generate-pdn)
   - [🧩 Loading PDN in Magic](#-loading-pdn-in-magic)
   - [🔍 PDN Visualization](#-pdn-visualization)
3. [🚦 Routing Process](#-routing)
   - [⚙️ Running Routing in OpenLANE](#️-running-routing-in-openlane)
   - [🖥️ Viewing Routed Design in Magic](#️-viewing-routed-design-in-magic)
   - [🔩 Metal Contacts and Fast Route Guide](#-metal-contacts-and-fast-route-guide)
4. [🧠 Post-Route Timing Analysis](#-post-route-timing-analysis)
   - [⚙️ Running OpenROAD for Timing](#️-running-openroad-for-timing)
   - [📉 Setup and Hold Slack Results](#-setup-and-hold-slack-results)
5. [🎬 Final Output](#-final-output-final-gdsii)
   - [📄 Final DEF File Structure](#-final-def-file-structure)
6. [🏆 Achievement Checklist](#-achievement-checklist)
7. [🎓 Key Takeaways](#-key-takeaways)
8. [📚 Repository & Author](#-repository--author)
---

## Objective

The objective of **Day 5 – Power Distribution Network (PDN) & Routing** is to complete the final physical implementation steps of the ASIC design using **OpenLANE** with the **Sky130 PDK**.  
This involves generating a robust **Power Distribution Network (PDN)** to ensure stable power delivery across all standard cells and performing **detailed routing** to connect all logic nets.  
The session focuses on executing OpenLANE commands for PDN creation, running **global and detailed routing**, and visualizing results using **Magic Layout**.  
It also includes **post-route timing analysis** using **OpenROAD** to verify design timing closure, ensuring that setup and hold constraints are satisfied after parasitic extraction.  
By completing this stage, the design achieves a **DRC-clean, fully routed layout** — ready for **GDSII generation** and tape-out.

---

# Power Distribution Network 
To create a power distribution network, lets start from scratch by creating a new OpenLane flow. Follow the commands given below

```bash 
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_STRATEGY) "DELAY 3"
set ::env(SYNTH_SIZING) 1
run_synthesis
init_floorplan
place_io
tap_decap_or
run_placement
unset ::env(LIB_CTS) # Not required unless you get an error
run_cts
gen_pdn 
```
- Commands to generate PDN
![docker](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command1.png)
![Commands 2](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command2.png)
![Commands 3](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command3.png)
![Commands 4](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command4.png)
![Commands 5](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command5.png)
![Commands 6](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command6.png)
![Commands 7](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command7.png)
![Commands 7](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command8.png)

To load the PDN on magic follow these commands
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/30-10-03-34/tmp/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

- Generated Power Distribution Network
![PDN](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command10.png)

- Zoomed in to view power and ground rails
![PWR and Gnd](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command11.png)
![STD cells](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command12.png)

# Routing 
After creating the power distribution network, our design is ready for routing. Follow the command given below 
```bash
run_routing
```
![Routing Success](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command13.png)

Once the routing is completed we can view it in magic by following these commands
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/30-10-03-34/results/routing/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

- Routed Design
![Routed magic output](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command14.png)

- Zommed in routed design to view metal contacts
![Metal Contacts](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command15.png)
![Metal Contacts](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command16.png)
![Metal Contacts](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command17.png)
- Fast route guide 
![Fast route guide](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command18.png)

In the updated version of OpenLane, extracting parsitics is included in the routing process so no need to run separate commands for it. 

- `.spef` File generation after routing
![spef file]()

# Post-route Timing Analysis
Once the routing of the `picorv32a` design is completed, we perform a timing analysis again in the OpenROad environment. To perform timing analysis carry out these steps

```bash 
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/30-10_03-34/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/30-10_03-34/results/routing/picorv32a.def
write_db pico_route.db
read_db pico_route.db
read_verilog /openLANE_flow/designs/picorv32a/runs/30-10_03-34/results/synthesis/picorv32a.synthesis_preroute.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
read_spef /openLANE_flow/designs/picorv32a/runs/30-10_03-34/results/routing/picorv32a.spef # Read SPEF
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
exit 
```

---
## 🎬 Final Output {#final-gdsii}

### 📄 Final DEF File Structure

The `picorv32a.def` file contains:

```
COMPONENTS <count>      # All placed cells
PINS <count>            # I/O connections
NETS <count>            # Routed nets
SPECIALNETS <count>     # Power/ground
VIAS <count>            # Inter-layer vias
```

![Final DEF File](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week6/Day5/Images/Command19.png)
The final GDS Picture is at " cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/11-11_19-35/results/routing/" there wil be one png file

---

## 🏆 Achievement Checklist

- [x] 📍 Placement optimized and verified
- [x] ⚡ PDN generated with proper grid
- [x] 🌐 Global routing completed
- [x] 🎯 Detailed routing with 0 DRC violations
- [x] ✨ Final GDSII ready for fabrication

---

## 🎓 Key Takeaways

| Concept | Why It Matters |
|---------|---------------|
| **PDN** | Powers every transistor reliably |
| **Global Routing** | Plans congestion-free paths |
| **Detailed Routing** | Produces manufacturable metal shapes |
| **DRC Clean** | Ensures fabrication success |

---
## 💡 Summary

In this session, we executed the **Power Distribution Network (PDN)** generation and **Routing** stages for the `picorv32a` RISC-V core using **OpenLANE** and **Sky130 PDK**.  
We initiated PDN creation with `gen_pdn`, visualized the resulting power and ground grids in **Magic**, and verified the presence of VDD/VSS rails across all standard cells.  
Following PDN setup, the **routing phase** was executed using `run_routing`, successfully completing both **global** and **detailed routing** without DRC violations.  
Post-routing, we performed **timing analysis in OpenROAD**, imported `.spef` parasitics, and confirmed that the design met setup and hold constraints.  
This marks the final step of the physical design flow, yielding a **clean routed layout** and preparing the project for **GDSII export and fabrication readiness**.

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
