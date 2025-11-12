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
In this document we will carry out the routing stage of PnR. It describes how how to incorporate the `Power Network` for the design then perform routing and visualise the final routed output on magic. It also talks about post-routing timing analysis to verify if the deisign meets timing requirements by utilizing design obtained from extracting parasitics.

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
![docker]()
![Commands 2]()
![Commands 3]()
![Commands 4]()
![Commands 5]()
![Commands 6]()
![Commands 7]()

To load the PDN on magic follow these commands
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/30-10-03-34/tmp/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```

- Generated Power Distribution Network
![PDN]()

- Zoomed in to view power and ground rails
![PWR and Gnd]()
![STD cells]()

# Routing 
After creating the power distribution network, our design is ready for routing. Follow the command given below 
```bash
run_routing
```
![Routing Run]()
![Routing Violations]()
![Routing Success]()

Once the routing is completed we can view it in magic by following these commands
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/30-10-03-34/results/routing/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```

- Routed Design
![Routed magic output]()

- Zommed in routed design to view metal contacts
![Metal Contacts]()

- Fast route guide 
![Fast route guide]()

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

- Running OpenRoad
![Running Openroad]()

- Post-route Hold Slack
![hold slack]()

- Post-route Setup Slack
![Setup slack]()
