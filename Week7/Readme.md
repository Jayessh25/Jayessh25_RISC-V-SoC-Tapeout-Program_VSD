# 🚀 Week 7 : – OpenROAD-Flow-Scripts: Physical Design, Placement,Floorplan,Post-Route SPEF and Verilog Netlist Generation for VSDBabySoC
<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-7-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Week7** –  is to execute the complete **RTL-to-GDSII** flow for the VSDBabySoC using the **OpenROAD-Flow-Scripts** framework on the Sky130HD PDK, starting from synthesis and progressing through floorplanning, placement, and clock tree synthesis. This includes configuring the design environment, resolving library and constraint issues, validating each stage using OpenROAD GUI tools, and performing timing, density, and structural checks after every major step. The goal is to generate a timing-clean, physically realizable layout by integrating standard-cell libraries, LEFs, GDS files, PLL/IP macros, and custom configuration settings, ultimately preparing the design for routing and final GDS export. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [📝 Summary](#-summary)
3. [📂 Design Setup & Directory Structure](#-design-setup--directory-structure)
4. [⚙️ Configuration File (config.mk)](#️-configuration-file-configmk)
5. [🔧 Fixing avsdpll.lib Error](#-fixing-avsdplllib-error)
6. [🏗️ Floorplanning](#-floorplanning)
7. [📐 Floorplan Visualization in OpenROAD](#-floorplan-visualization-in-openroad)
8. [📍 Placement](#-placement)
9. [🌡️ Density & Pin Heatmaps](#-density--pin-heatmaps)
10. [⏰ Clock Tree Synthesis (CTS)](#-clock-tree-synthesis-cts)
11. [🌳 Clock Tree Viewer Analysis](#-clock-tree-viewer-analysis)
12. [🧪 Timing Verification](#-timing-verification)
13. [📦 Final Files & DEF/GDS Checks](#-final-files--defgds-checks)
14. [📚 Repository & Author](#-repository--author)

---

## Objective
The objective of Week 7 is to execute the complete RTL-to-GDSII flow for the VSDBabySoC using the OpenROAD-Flow-Scripts framework on the Sky130HD PDK, starting from synthesis and progressing through floorplanning, placement, and clock tree synthesis. This includes configuring the design environment, resolving library and constraint issues, validating each stage using OpenROAD GUI tools, and performing timing, density, and structural checks after every major step. The goal is to generate a timing-clean, physically realizable layout by integrating standard-cell libraries, LEFs, GDS files, PLL/IP macros, and custom configuration settings, ultimately preparing the design for routing and final GDS export.

---

###  `RTL2GDS Flow for VSDBabySoC: Initial Steps`

1. **Create Directories:**
   - Inside `OpenROAD-flow-scripts/flow/designs/sky130hd/`, create a folder named `vsdbabysoc`.
   - Create another folder named `vsdbabysoc` in `OpenROAD-flow-scripts/flow/designs/src/` and place all Verilog files here.

2. **Copy Folders:**
   - From your `VSDBabySoC` folder, copy the following folders into `sky130hd/vsdbabysoc`:
     - **gds:** Contains `avsddac.gds`, `avsdpll.gds`.
     - **include:** Contains `sandpiper.vh`, `sandpiper_gen.vh`, `sp_default.vh`, `sp_verilog.vh`.
     - **lef:** Contains `avsddac.lef`, `avsdpll.lef`.
     - **lib:** Contains `avsddac.lib`, `avsdpll.lib`.

3. **Copy Constraint and Configuration Files:**
   - Copy `vsdbabysoc_synthesis.sdc` into `sky130hd/vsdbabysoc`.
   - Copy `macro.cfg` and `pin_order.cfg` into `sky130hd/vsdbabysoc`.

4. **Create Config File:**
   - Create a `config.mk` file in `sky130hd/vsdbabysoc` with the required configuration details. 

### config.mk

```
   export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME     = vsdbabysoc
export PLATFORM        = sky130hd

export VSDBabySoC_DIR = /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)


export VERILOG_FILES = \
    $(VSDBabySoC_DIR)/src/vsdbabysoc.v \
    $(VSDBabySoC_DIR)/src/rvmyth.v \
    $(VSDBabySoC_DIR)/src/clk_gate.v

export SDC_FILE = $(VSDBabySoC_DIR)/vsdbabysoc_synthesis.sdc
export VERILOG_INCLUDE_DIRS = $(wildcard $(VSDBabySoC_DIR)/include/)

export ADDITIONAL_LIBS = \
    $(VSDBabySoC_DIR)/lib/avsddac.lib \
    $(VSDBabySoC_DIR)/lib/avsdpll.lib

export ADDITIONAL_GDS  = $(wildcard $(VSDBabySoC_DIR)/gds/*.gds)
export ADDITIONAL_LEFS = $(wildcard $(VSDBabySoC_DIR)/lef/*.lef)

export ADDITIONAL_ROUTING_BLOCKAGES = $(VSDBabySoC_DIR)/route_blockages.tcl

export CLOCK_PORT = CLK
export CLOCK_NET  = CLK

export FP_PIN_ORDER_CFG = $(VSDBabySoC_DIR)/pin_order.cfg

export DIE_AREA  = 0   0   1600 1600
export CORE_AREA = 20  20  1580 1580

export FP_CORE_UTIL    = 40
export PLACE_DENSITY   = 0.30   # Changed from 0.48

export MACRO_PLACE_HALO    = 40 40  # Changed from 20 20
export MACRO_PLACE_CHANNEL = 40 40

export RTLMP_FLOW = 0
export MACRO_PLACEMENT = $(VSDBabySoC_DIR)/macro.cfg

 #dac 320 900 N
 #pll 40  40  N

export CTS_BUF_DISTANCE  = 600
export SKIP_GATE_CLONING = 1

# Normal congestion settings
export GRT_ALLOW_CONGESTION      = 1
export GRT_SKIP_CONGESTION_CHECK = 0
export GRT_CONGESTION_DRIVEN     = 1
export GRT_MAX_ITERATIONS        = 700
export GRT_ADJUSTMENT            = 0.15
export GRT_VIA_COST_ADJUSTMENT   = 3.0
export GRT_LAYER_ADJUSTMENTS     = {met1:0.25,met2:0.20,met3:0.10,met4:0.05,met5:0.00}
export GRT_MAX_TIME              = 3600

# ---- REQUIRED FIXES FOR YOUR VERSION (prevents GRT-0116 stop) ----

export GRT_OVERFLOW_ITERS   = 300

export TNS_END_PERCENT      = 100
export REMOVE_ABC_BUFFERS   = 1
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS    = 1
export GRT_FAIL_ON_OVERFLOW = 0
export RCX_EXTRACT=1
export RCX_CORNER=typical
export RCX_SPEF_OUTPUT=1
export SPEF_OUTPUT_FILE = $(VSDBabySoC_DIR)/vsdbabysoc.spef

```
</details>

This script sets up environment variables and configurations for the design and synthesis of a System-on-Chip (SoC) using the OpenROAD flow. The design is based on the "vsdbabysoc" and targets the "sky130hd" platform.

--------


### `File Structure After Setup`

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/src/vsdbabysoc/
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command2.png)

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ ls -ltrh designs/sky130hd/vsdbabysoc/
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command1.png)

#### Now go to terminal and run the following commands:

```shell
# Navigate to the OpenROAD flow scripts directory
cd OpenROAD-flow-scripts
# Source the environment setup script
source env.sh
# Change to the flow directory
cd flow
```
----
 
### `Run Synthesis`

```shell
# Ensure you are in the 'flow' directory before running the synthesis command
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```

This command runs the synthesis process using the specified design configuration file `config.mk` for the `vsdbabysoc` design on the `sky130hd` platform.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command3.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command4.png)

#### Synthesis netlist

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim results/sky130hd/vsdbabysoc/base/1_1_yosys.v
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command5.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command6.png)

#### Synthesis log

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim logs/sky130hd/vsdbabysoc/base/1_1_yosys.log
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command5.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command7.png)

#### Synthesis Check

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim reports/sky130hd/vsdbabysoc/base/synth_check.txt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command8.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command9.png)

#### Synthesis Stats

```shell
jayesshsk@jayesshsk:~/OpenROAD-flow-scripts/flow$ gvim reports/sky130hd/vsdbabysoc/base/synth_stat.txt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command11.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command12.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command13.png)

<details> <summary><strong>synth_stat.txt</strong></summary>

```

20. Printing statistics.

=== vsdbabysoc ===

        +----------Local Count, excluding submodules.
        |        +-Local Area, excluding submodules.
        |        | 
     6715        - wires
     6715        - wire bits
     1285        - public wires
     1285        - public wire bits
        7        - ports
        7        - port bits
     6605 5.29E+04 cells
        1        -   avsddac
        1        -   avsdpll
        1   11.261   sky130_fd_sc_hd__a2111o_1
        6    52.55   sky130_fd_sc_hd__a2111oi_0
        8   70.067   sky130_fd_sc_hd__a211o_1
       26  195.187   sky130_fd_sc_hd__a211oi_1
       17  127.622   sky130_fd_sc_hd__a21boi_0
       31  232.723   sky130_fd_sc_hd__a21o_1
      884 4.42E+03   sky130_fd_sc_hd__a21oi_1
        7   61.309   sky130_fd_sc_hd__a21oi_2
       15  150.144   sky130_fd_sc_hd__a221o_1
       37  324.061   sky130_fd_sc_hd__a221oi_1
       24  210.202   sky130_fd_sc_hd__a22o_1
      222 1.67E+03   sky130_fd_sc_hd__a22oi_1
        1    21.27   sky130_fd_sc_hd__a22oi_4
        1   11.261   sky130_fd_sc_hd__a2bb2o_2
        4   35.034   sky130_fd_sc_hd__a2bb2oi_1
        2   20.019   sky130_fd_sc_hd__a311o_1
       15  131.376   sky130_fd_sc_hd__a311oi_1
        8   70.067   sky130_fd_sc_hd__a31o_2
       53  331.568   sky130_fd_sc_hd__a31oi_1
        1    10.01   sky130_fd_sc_hd__a32o_1
        3   26.275   sky130_fd_sc_hd__a32oi_1
        3   26.275   sky130_fd_sc_hd__a41oi_1
        2   12.512   sky130_fd_sc_hd__and2_0
       10    62.56   sky130_fd_sc_hd__and2_1
       14   87.584   sky130_fd_sc_hd__and3_1
       34  127.622   sky130_fd_sc_hd__buf_1
        9   45.043   sky130_fd_sc_hd__buf_2
        1    7.507   sky130_fd_sc_hd__buf_4
        3   33.782   sky130_fd_sc_hd__buf_6
      548 2.06E+03   sky130_fd_sc_hd__clkbuf_1
        4   15.014   sky130_fd_sc_hd__clkinv_1
        1    3.754   sky130_fd_sc_hd__conb_1
     1144 2.29E+04   sky130_fd_sc_hd__dfxtp_1
        4   80.077   sky130_fd_sc_hd__fa_1
      100   1251.2   sky130_fd_sc_hd__ha_1
      104  390.374   sky130_fd_sc_hd__inv_1
       56  630.605   sky130_fd_sc_hd__mux2_2
       92  920.883   sky130_fd_sc_hd__mux2i_1
        1   22.522   sky130_fd_sc_hd__mux2i_4
       69  1553.99   sky130_fd_sc_hd__mux4_2
     1461  5484.01   sky130_fd_sc_hd__nand2_1
       28  175.168   sky130_fd_sc_hd__nand2b_1
      213 1.07E+03   sky130_fd_sc_hd__nand3_1
       40  300.288   sky130_fd_sc_hd__nand3b_1
       70   437.92   sky130_fd_sc_hd__nand4_1
        2   17.517   sky130_fd_sc_hd__nand4b_1
      284 1.07E+03   sky130_fd_sc_hd__nor2_1
       52  325.312   sky130_fd_sc_hd__nor2b_1
       74  370.355   sky130_fd_sc_hd__nor3_1
        9   67.565   sky130_fd_sc_hd__nor3b_1
        1   12.512   sky130_fd_sc_hd__nor3b_2
       25    156.4   sky130_fd_sc_hd__nor4_1
        1    8.758   sky130_fd_sc_hd__nor4b_1
        1   11.261   sky130_fd_sc_hd__o2111a_1
        8   70.067   sky130_fd_sc_hd__o2111ai_1
        3   30.029   sky130_fd_sc_hd__o211a_1
       51  382.867   sky130_fd_sc_hd__o211ai_1
       30  225.216   sky130_fd_sc_hd__o21a_1
      397 1.99E+03   sky130_fd_sc_hd__o21ai_0
        8   40.038   sky130_fd_sc_hd__o21ai_1
       10   75.072   sky130_fd_sc_hd__o21bai_1
       27  236.477   sky130_fd_sc_hd__o221ai_1
       36  315.302   sky130_fd_sc_hd__o22a_1
       31  193.936   sky130_fd_sc_hd__o22ai_1
        2   17.517   sky130_fd_sc_hd__o2bb2ai_1
        1    10.01   sky130_fd_sc_hd__o311a_1
        6    52.55   sky130_fd_sc_hd__o311ai_0
        5   43.792   sky130_fd_sc_hd__o31a_1
       34  255.245   sky130_fd_sc_hd__o31ai_1
        1   12.512   sky130_fd_sc_hd__o31ai_2
        2   20.019   sky130_fd_sc_hd__o32a_1
        4   35.034   sky130_fd_sc_hd__o32ai_1
        1   11.261   sky130_fd_sc_hd__o41a_1
        5   43.792   sky130_fd_sc_hd__o41ai_1
        2   12.512   sky130_fd_sc_hd__or2_0
        1    6.256   sky130_fd_sc_hd__or2_1
        8   50.048   sky130_fd_sc_hd__or2_2
       28  175.168   sky130_fd_sc_hd__or3_1
        1    8.758   sky130_fd_sc_hd__or3b_1
        2   17.517   sky130_fd_sc_hd__or3b_2
        4   30.029   sky130_fd_sc_hd__or4_1
       47  411.645   sky130_fd_sc_hd__xnor2_1
       22  192.685   sky130_fd_sc_hd__xor2_1

   Area for cell type \avsdpll is unknown!
   Area for cell type \avsddac is unknown!

   Chip area for module '\vsdbabysoc': 52874.460800
     of which used for sequential elements: 22901.964800 (43.31%)

```
</details>

----------

### `Run Floorplan`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```

This command initiates the floorplanning process for the `vsdbabysoc` design using the specified configuration file `config.mk` on the `sky130hd` platform.

#### Floorplan Error and Fix

❗**Note:** You may encounter the following error:

```shell
[ERROR STA-0164] .../vsdbabysoc/lib/avsdpll.lib line 54, syntax error
Error: floorplan.tcl, 4 STA-0164
```

**Fix:**
This error is caused by commented block structures in your Liberty file avsdpll.lib. OpenROAD’s parser does not tolerate partially commented blocks like:

```shell
//pin (GND#2) {
//  direction : input;
//  max_transition : 2.5;
//  capacitance : 0.001;
//}
```

✅ To fix it, simply delete the entire commented block starting at line 54:

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command14.png)

After saving the changes, re-run the floorplan step and the flow should proceed without syntax errors. 

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command15.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command16.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command17.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command18.png)




#### Floorplan Result (GUI)

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command19.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command20.png)


------

### `run placement`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command21.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command22.png)


**Placement Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```

This image shows the placement stage in OpenROAD with the **placement density** heat map enabled.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command23.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command24.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command25.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command26.png)
This image shows a zoomed-in view of the Placement Density Heatmap after the placement stage:

- **Red regions** indicate areas with higher cell density, approaching 100%.
- **Green and blue regions** indicate moderate to low cell density.
- The highlighted row (`ROW_343`) displays details such as origin coordinates, site count, site spacing, and bounding box dimensions.

❗**Note:** In the floorplan stage, you do not see any placement density heat maps because standard cells have not yet been placed. The heat map will only appear after the placement step.

<ins>The placement density percentage is calculated as:</ins>

**Placement Density (%) = (Area Occupied by Cells ÷ Total Placement Area) × 100**

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command27.png)

This image shows the **Pin Density Heatmap** after the placement stage.

<ins>The pin density percentage is calculated as:</ins>

**Pin Density (%) = (Number of Pins in a Region ÷ Total Area of that Region) × 100**

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command28.png)

### `run cts`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command29.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command30.png)

**CTS Result (GUI)**

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
```

This image shows the Clock Tree Synthesis (CTS) stage, highlighting a placed clock buffer (clkbuf_leaf_209_CLK) with its properties displayed in the Inspector, including position, orientation, and connectivity details.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command31.png)

This image shows the **Clock Tree Viewer** after CTS, illustrating the clock buffer distribution on the layout and a histogram of clock insertion delays, indicating balanced clock skew across the sinks.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command32.png)

This image shows the **Clock Tree Viewer after Clock Tree Synthesis (CTS)**, illustrating the hierarchical structure of the clock network. The root node at the top represents the clock source (`pll/CLK`), and the branches show the inserted clock buffers used to distribute the clock signal across the design. The vertical axis represents the **clock arrival times (in nanoseconds)** at each stage. The endpoints at the bottom represent the registers (clock sinks), where the clock reaches after passing through multiple buffer levels. The balanced branching and closely aligned arrival times indicate **low clock skew and a well-optimized clock tree**.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command33.png)

This image shows the **Setup Timing Report**, presenting a list of timing paths with key metrics such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Logic Depth**
- **Fanout**

All paths have **positive slack**, confirming that the design meets **setup timing requirements**.


This image displays the **Hold Timing Report**, showing timing paths with details such as:

- **Required Time**
- **Arrival Time**
- **Slack**
- **Skew**
- **Logic Delay**
- **Fanout**

All paths listed have **positive slack**, indicating that the design meets **hold timing requirements** and is free from hold violations.


This image shows the **Setup Slack Histogram** after CTS. The histogram represents the distribution of endpoint slack values, all of which are positive, indicating that there are no setup timing violations.
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command37.png)

This image shows the **Hold Slack Histogram** after CTS. The histogram represents the distribution of hold slack values for all endpoints. All values are positive, confirming that the design meets hold timing requirements without any violations.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command36.png)
Zoomed-in view of the design after CTS, showing inserted clock buffers and routing connections.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command38.png)

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command39.png)

**CTS final report:**

```shell
gvim /home/jayesshsk@jayesshsk/OpenROAD-flow-scripts/flow/reports/sky130hd/vsdbabysoc/base/4_cts_final.rpt
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command40.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command41.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command42.png)

<details> <summary><strong>4_cts_final.rpt</strong></summary>

```


==========================================================================
cts final report_tns
--------------------------------------------------------------------------
tns max 0.00

==========================================================================
cts final report_wns
--------------------------------------------------------------------------
wns max 0.00

==========================================================================
cts final report_worst_slack
--------------------------------------------------------------------------
worst slack max 6.28

==========================================================================
cts final report_clock_min_period
--------------------------------------------------------------------------
clk period_min = 4.72 fmax = 211.74

==========================================================================
cts final report_clock_skew
--------------------------------------------------------------------------
Clock clk
   1.05 source latency core.CPU_dmem_rd_data_a5[21]$_DFF_P_/CLK ^
  -0.85 target latency core.CPU_Xreg_value_a4[17][21]$_SDFFE_PP0P_/CLK ^
   0.00 CRPR
--------------
   0.20 setup skew


==========================================================================
cts final report_checks -path_delay min
--------------------------------------------------------------------------
Startpoint: core.CPU_rd_a4[4]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_rd_a5[4]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.02    0.37 ^ clkbuf_4_6_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.16    0.18    0.32    0.70 ^ clkbuf_4_6_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_6_0_CLK (net)
                  0.18    0.00    0.70 ^ clkbuf_leaf_95_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.05    0.07    0.19    0.89 ^ clkbuf_leaf_95_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_95_CLK (net)
                  0.07    0.00    0.89 ^ core.CPU_rd_a4[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     1    0.00    0.05    0.31    1.20 ^ core.CPU_rd_a4[4]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_rd_a4[4] (net)
                  0.05    0.00    1.20 ^ core.CPU_rd_a5[4]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  1.20   data arrival time

                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_7_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.19    0.20    0.34    0.71 ^ clkbuf_4_7_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_7_0_CLK (net)
                  0.20    0.00    0.72 ^ clkbuf_leaf_89_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.04    0.06    0.20    0.91 ^ clkbuf_leaf_89_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_89_CLK (net)
                  0.06    0.00    0.91 ^ core.CPU_rd_a5[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00    0.91   clock reconvergence pessimism
                         -0.03    0.89   library hold time
                                  0.89   data required time
-----------------------------------------------------------------------------
                                  0.89   data required time
                                 -1.20   data arrival time
-----------------------------------------------------------------------------
                                  0.32   slack (MET)



==========================================================================
cts final report_checks -path_delay max
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_3_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.15    0.17    0.31    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_3_0_CLK (net)
                  0.17    0.00    0.69 ^ clkbuf_leaf_59_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.05    0.07    0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_59_CLK (net)
                  0.07    0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     7    0.04    0.20    0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src1_value_a3[14] (net)
                  0.20    0.00    1.33 v _10836_/A (sky130_fd_sc_hd__ha_1)
    12    0.06    0.47    0.65    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00110_ (net)
                  0.47    0.00    1.98 ^ _05444_/A (sky130_fd_sc_hd__nand4_1)
     6    0.02    0.30    0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01127_ (net)
                  0.30    0.00    2.33 v _08027_/A (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.36    0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.36    0.00    2.78 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.01    0.25    0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.25    0.00    3.10 v place394/A (sky130_fd_sc_hd__buf_4)
     2    0.01    0.03    0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
                                         net393 (net)
                  0.03    0.00    3.32 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.77    0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.77    0.00    3.93 ^ _08381_/A3 (sky130_fd_sc_hd__a31oi_1)
     1    0.01    0.15    0.20    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
                                         _03454_ (net)
                  0.15    0.00    4.14 v _08382_/S (sky130_fd_sc_hd__mux2i_1)
     1    0.00    0.13    0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
                                         _03455_ (net)
                  0.13    0.00    4.33 ^ _08384_/B (sky130_fd_sc_hd__nand3b_1)
     4    0.01    0.15    0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
                                         _03457_ (net)
                  0.15    0.00    4.49 v _08401_/A1 (sky130_fd_sc_hd__o21ai_1)
    11    0.04    0.84    0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _03474_ (net)
                  0.84    0.00    5.20 ^ _08956_/A (sky130_fd_sc_hd__nand3_1)
     1    0.00    0.17    0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
                                         _03932_ (net)
                  0.17    0.00    5.36 v _08958_/A1 (sky130_fd_sc_hd__a21oi_1)
     1    0.00    0.13    0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
                                         _00868_ (net)
                  0.13    0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.53   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.22    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01   11.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03   11.38 ^ clkbuf_4_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.15    0.16    0.31   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_0_0_CLK (net)
                  0.16    0.00   11.69 ^ clkbuf_leaf_76_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_76_CLK (net)
                  0.06    0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.88   clock reconvergence pessimism
                         -0.07   11.81   library setup time
                                 11.81   data required time
-----------------------------------------------------------------------------
                                 11.81   data required time
                                 -5.53   data arrival time
-----------------------------------------------------------------------------
                                  6.28   slack (MET)



==========================================================================
cts final report_checks -unconstrained
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.22    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01    0.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03    0.38 ^ clkbuf_4_3_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.15    0.17    0.31    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_3_0_CLK (net)
                  0.17    0.00    0.69 ^ clkbuf_leaf_59_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.05    0.07    0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_59_CLK (net)
                  0.07    0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     7    0.04    0.20    0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src1_value_a3[14] (net)
                  0.20    0.00    1.33 v _10836_/A (sky130_fd_sc_hd__ha_1)
    12    0.06    0.47    0.65    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
                                         _00110_ (net)
                  0.47    0.00    1.98 ^ _05444_/A (sky130_fd_sc_hd__nand4_1)
     6    0.02    0.30    0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
                                         _01127_ (net)
                  0.30    0.00    2.33 v _08027_/A (sky130_fd_sc_hd__nor4_1)
     1    0.01    0.36    0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
                                         _03108_ (net)
                  0.36    0.00    2.78 ^ _08028_/C1 (sky130_fd_sc_hd__o311ai_0)
     1    0.01    0.25    0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
                                         _03109_ (net)
                  0.25    0.00    3.10 v place394/A (sky130_fd_sc_hd__buf_4)
     2    0.01    0.03    0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
                                         net393 (net)
                  0.03    0.00    3.32 v _08206_/A1 (sky130_fd_sc_hd__a311oi_1)
     3    0.02    0.77    0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
                                         _03283_ (net)
                  0.77    0.00    3.93 ^ _08381_/A3 (sky130_fd_sc_hd__a31oi_1)
     1    0.01    0.15    0.20    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
                                         _03454_ (net)
                  0.15    0.00    4.14 v _08382_/S (sky130_fd_sc_hd__mux2i_1)
     1    0.00    0.13    0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
                                         _03455_ (net)
                  0.13    0.00    4.33 ^ _08384_/B (sky130_fd_sc_hd__nand3b_1)
     4    0.01    0.15    0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
                                         _03457_ (net)
                  0.15    0.00    4.49 v _08401_/A1 (sky130_fd_sc_hd__o21ai_1)
    11    0.04    0.84    0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _03474_ (net)
                  0.84    0.00    5.20 ^ _08956_/A (sky130_fd_sc_hd__nand3_1)
     1    0.00    0.17    0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
                                         _03932_ (net)
                  0.17    0.00    5.36 v _08958_/A1 (sky130_fd_sc_hd__a21oi_1)
     1    0.00    0.13    0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
                                         _00868_ (net)
                  0.13    0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.53   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.22    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.03    0.01   11.01 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    16    0.35    0.36    0.34   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.36    0.03   11.38 ^ clkbuf_4_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.15    0.16    0.31   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_0_0_CLK (net)
                  0.16    0.00   11.69 ^ clkbuf_leaf_76_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_76_CLK (net)
                  0.06    0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   11.88   clock reconvergence pessimism
                         -0.07   11.81   library setup time
                                 11.81   data required time
-----------------------------------------------------------------------------
                                 11.81   data required time
                                 -5.53   data arrival time
-----------------------------------------------------------------------------
                                  6.28   slack (MET)



==========================================================================
cts final report_check_types -max_slew -max_cap -max_fanout -violators
--------------------------------------------------------------------------

==========================================================================
cts final max_slew_check_slack
--------------------------------------------------------------------------
0.21570834517478943

==========================================================================
cts final max_slew_check_limit
--------------------------------------------------------------------------
1.4979510307312012

==========================================================================
cts final max_slew_check_slack_limit
--------------------------------------------------------------------------
0.1440

==========================================================================
cts final max_fanout_check_slack
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_fanout_check_limit
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_capacitance_check_slack
--------------------------------------------------------------------------
0.0062514557503163815

==========================================================================
cts final max_capacitance_check_limit
--------------------------------------------------------------------------
0.021067000925540924

==========================================================================
cts final max_capacitance_check_slack_limit
--------------------------------------------------------------------------
0.2967

==========================================================================
cts final max_slew_violation_count
--------------------------------------------------------------------------
max slew violation count 0

==========================================================================
cts final max_fanout_violation_count
--------------------------------------------------------------------------
max fanout violation count 0

==========================================================================
cts final max_cap_violation_count
--------------------------------------------------------------------------
max cap violation count 0

==========================================================================
cts final setup_violation_count
--------------------------------------------------------------------------
setup violation count 0

==========================================================================
cts final hold_violation_count
--------------------------------------------------------------------------
hold violation count 0

==========================================================================
cts final report_checks -path_delay max reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_src1_value_a3[14]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34    0.69 ^ clkbuf_4_3_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.19    0.88 ^ clkbuf_leaf_59_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.88 ^ core.CPU_src1_value_a3[14]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.44    1.33 v core.CPU_src1_value_a3[14]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.66    1.98 ^ _10836_/SUM (sky130_fd_sc_hd__ha_1)
   0.34    2.33 v _05444_/Y (sky130_fd_sc_hd__nand4_1)
   0.46    2.78 ^ _08027_/Y (sky130_fd_sc_hd__nor4_1)
   0.31    3.10 v _08028_/Y (sky130_fd_sc_hd__o311ai_0)
   0.22    3.32 v place394/X (sky130_fd_sc_hd__buf_4)
   0.61    3.93 ^ _08206_/Y (sky130_fd_sc_hd__a311oi_1)
   0.21    4.14 v _08381_/Y (sky130_fd_sc_hd__a31oi_1)
   0.19    4.33 ^ _08382_/Y (sky130_fd_sc_hd__mux2i_1)
   0.17    4.49 v _08384_/Y (sky130_fd_sc_hd__nand3b_1)
   0.71    5.20 ^ _08401_/Y (sky130_fd_sc_hd__o21ai_1)
   0.16    5.36 v _08956_/Y (sky130_fd_sc_hd__nand3_1)
   0.17    5.53 ^ _08958_/Y (sky130_fd_sc_hd__a21oi_1)
   0.00    5.53 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
           5.53   data arrival time

  11.00   11.00   clock clk (rise edge)
   0.00   11.00   clock source latency
   0.00   11.00 ^ pll/CLK (avsdpll)
   0.35   11.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34   11.69 ^ clkbuf_4_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.19   11.88 ^ clkbuf_leaf_76_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00   11.88 ^ core.CPU_Xreg_value_a4[17][28]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00   11.88   clock reconvergence pessimism
  -0.07   11.81   library setup time
          11.81   data required time
---------------------------------------------------------
          11.81   data required time
          -5.53   data arrival time
---------------------------------------------------------
           6.28   slack (MET)



==========================================================================
cts final report_checks -path_delay min reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_rd_a4[4]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_rd_a5[4]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.35    0.70 ^ clkbuf_4_6_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.89 ^ clkbuf_leaf_95_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.89 ^ core.CPU_rd_a4[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.31    1.20 ^ core.CPU_rd_a4[4]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.00    1.20 ^ core.CPU_rd_a5[4]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           1.20   data arrival time

   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.35    0.35 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.36    0.71 ^ clkbuf_4_7_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.91 ^ clkbuf_leaf_89_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.91 ^ core.CPU_rd_a5[4]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00    0.91   clock reconvergence pessimism
  -0.03    0.89   library hold time
           0.89   data required time
---------------------------------------------------------
           0.89   data required time
          -1.20   data arrival time
---------------------------------------------------------
           0.32   slack (MET)



==========================================================================
cts final critical path target clock latency max path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path target clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path source clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path delay
--------------------------------------------------------------------------
5.5293

==========================================================================
cts final critical path slack
--------------------------------------------------------------------------
6.2773

==========================================================================
cts final slack div critical path delay
--------------------------------------------------------------------------
113.527933

==========================================================================
cts final report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             4.37e-03   4.00e-04   9.26e-09   4.77e-03  36.9%
Combinational          8.88e-04   2.29e-03   1.00e-08   3.18e-03  24.6%
Clock                  2.54e-03   2.44e-03   2.16e-09   4.98e-03  38.5%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  7.79e-03   5.13e-03   2.15e-08   1.29e-02 100.0%
                          60.3%      39.7%       0.0%
```
</details>


### `run routing`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command43.png)
![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command44.png)


# VSDBabySoC Global Routing Failure: Root Cause Analysis & Resolution

A VSDBabySoC design using OpenROAD-flow-scripts with Sky130 PDK was failing at the global routing stage (5_1_grt) with congestion overflow errors. The root cause was identified as **pin accessibility obstructions in the analog macro LEF files**, specifically metal layer obstructions blocking router access to macro pins.

---

## 1. Initial Problem Statement

### Error Message

```bash
[ERROR GRT-0116] Global routing finished with congestion.
Check the congestion regions in the DRC Viewer.
```

### Symptoms

* Global routing completed but reported overflow violations
* Total overflow count: 18 violations across met3, met4, and met5
* Design utilization was only 13% (not a density issue)
* Routing resources appeared adequate (~1-2% usage on most layers)

---

## 2. Investigation Process

### Phase 1: Initial Configuration Analysis

The original `config.mk` had aggressive routing settings:

```makefile
export PLACE_DENSITY   = 0.30
export MACRO_PLACE_HALO    = 20 20
export MACRO_PLACE_CHANNEL = 40 40
export GRT_LAYER_ADJUSTMENTS = {met1:0.15,met2:0.12,met3:0.10,met4:0.08,met5:0.05}
```

These settings appeared reasonable for a 13% utilization design.

### Phase 2: Congestion Report Analysis

The congestion report revealed the critical insight:

```rpt
violation type: Horizontal congestion
    srcs: net:RV_TO_DAC\[9\]
    comment: capacity:0 usage:1 overflow:1
    bbox = (1090.2000, 579.6000) - (1097.1000, 586.5000) on Layer -
```

**Key observation:** `capacity:0` indicated the router had **zero legal routing tracks** in these regions, not just high congestion.

### Phase 3: LEF File Inspection

Examining `avsddac.lef` revealed the root cause:

#### Problem 1: OUT Pin Access Blocked by met4 OBS

```lef
PIN OUT
    PORT
      LAYER met4 ;
        RECT 1172.680 1127.710 1173.730 1175.360 ;
    END
END OUT

OBS
    LAYER met4 ;
        RECT 154.520 1171.010 1172.280 1175.050 ;  ← Ends at x=1172.280
        RECT 1174.130 1127.310 1188.200 1175.050 ; ← Starts at x=1174.130
```

The OUT pin (x: 1172.680-1173.730) was squeezed between obstructions with only ~0.4µm clearance on each side—insufficient for routing.

#### Problem 2: D[8] and D[9] Pins Only on li1 Layer

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
    END

PIN D[9]
    PORT
      LAYER li1 ;
        RECT 1150.220 613.050 1151.250 755.620 ;
    END
```

The li1 layer has effectively **zero routing capacity** in Sky130:

```lef
li1        Vertical            0            10          0.00%
```

#### Problem 3: Massive met1 Obstruction

```lef
LAYER met1 ;
    RECT 140.750 613.050 1313.360 1168.430 ;  ← Covers entire macro interior
```

This blocked any met1 access to the li1-only pins.

---

## 3. Resolution Strategy

### Fix 1: Widen met4 OBS Gap for OUT Pin

**Before:**

```lef
LAYER met4 ;
    RECT 154.520 1171.010 1172.280 1175.050 ;
    RECT 1174.130 1127.310 1188.200 1175.050 ;
```

**After:**

```lef
LAYER met4 ;
    RECT 154.520 1171.010 1168.000 1175.050 ;  ← Moved left edge back
    RECT 1178.000 1127.310 1188.200 1175.050 ; ← Moved right edge forward
```

This created a ~10µm routing corridor around the OUT pin instead of ~1.85µm.

### Fix 2: Add met1 Access Points for D[8] and D[9] Pins

**Before:**

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
    END
END D[8]
```

**After:**

```lef
PIN D[8]
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
      LAYER met1 ;
        RECT 1115.000 608.000 1120.000 613.050 ;  ← NEW: Access below OBS
    END
END D[8]
```

The new met1 shapes extend **below** y=613.050, outside the met1 obstruction boundary, giving the router legal access paths.

---

## 4. Technical Explanation

### Why This Happens

Analog macros (DAC, PLL) are often created with:

1. **Pins on lower metal layers (li1)** for analog signal integrity
2. **Large obstructions** to protect internal routing from interference
3. **Minimal consideration for digital router requirements**

OpenROAD's TritonRoute requires:

1. Legal access points to every pin
2. Sufficient routing capacity on accessible layers
3. Clear paths from pin access points to the routing grid

### The Pin Access Problem Illustrated

```text
                    ┌─────────────────────────────────┐
                    │         met1 OBS                │
                    │   (blocks entire interior)      │
                    │                                 │
    Router cannot   │    ┌──────┐                     │
    reach pin! ─────│───►│D[8]  │ (li1 only)          │
                    │    │ pin  │                     │
                    │    └──────┘                     │
                    │                                 │
                    └─────────────────────────────────┘
                    y = 613.050 ─────────────────────────

    SOLUTION: Add met1 shape BELOW y=613.050

                    ┌─────────────────────────────────┐
                    │         met1 OBS                │
                    │                                 │
                    │    ┌──────┐                     │
                    │    │D[8]  │ (li1)               │
                    │    │ pin  │                     │
                    │    └─┬────┘                     │
                    └──────│──────────────────────────┘
                    ═══════╪═══════  y = 613.050
                        ┌──┴───┐
    Router can now ────►│ met1 │ (new access point)
    reach pin!          │access│
                        └──────┘
```

---

## 5. Results

### Before Fix

```bash
[ERROR GRT-0116] Global routing finished with congestion.
Total Overflow: 29 violations
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week7/Images/Command44.png)


### After Fix

```bash
[INFO GRT-0018] Total wirelength: 407106 um
[INFO GRT-0014] Routed nets: 6450
[INFO ANT-0002] Found 0 net violations.
[INFO ANT-0001] Found 0 pin violations.
Design area 728556 um^2 13% utilization.
```
* Finally, the routing was completed successfully without congestion errors: After correcting the config.mk & avsddac.lef

* Final routing Screenshots:

![alt text]()

---

## 6. Key Lessons Learned

### For Physical Design Engineers

1. **Always verify macro pin accessibility** - Check that pins have routing access on layers the router can use

2. **Understand layer routing capacity** - li1 in Sky130 has near-zero routing capacity; pins must have met1+ access

3. **Inspect OBS sections carefully** - Obstructions can inadvertently block pin access even with sufficient apparent clearance

4. **Use congestion reports effectively** - `capacity:0` indicates physical blockage, not just high utilization

### For Analog Macro Designers

1. **Provide multi-layer pin definitions** - Include met1 or met2 access shapes for all pins

2. **Create routing channels** - Leave gaps in obstructions near pin locations

3. **Document routing requirements** - Specify minimum halo sizes and routing layer requirements

---

## 7. Verification Commands

To verify the fix worked:

```bash
# View the routed design
make gui_route DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk

# Check for DRC violations
make drc DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk

# Generate final reports
make report DESIGN_CONFIG=designs/sky130hd/VSDBabySoC/config.mk
```

---

## 8. Files Modified

| File | Change |
|------|--------|
| `avsddac.lef` | Widened met4 OBS gap around OUT pin; Added met1 access shapes to D[8], D[9] pins |
| `config.mk` | Increased MACRO_PLACE_HALO and MACRO_PLACE_CHANNEL  |

Modified Files of `avsddac.lef` and config.mk
If u want u can open below

<details>
<summary><strong>config.mk</strong></summary>
  
```shell
export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME     = vsdbabysoc
export PLATFORM        = sky130hd

export VSDBabySoC_DIR = /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)


export VERILOG_FILES = \
    $(VSDBabySoC_DIR)/src/vsdbabysoc.v \
    $(VSDBabySoC_DIR)/src/rvmyth.v \
    $(VSDBabySoC_DIR)/src/clk_gate.v

export SDC_FILE = $(VSDBabySoC_DIR)/vsdbabysoc_synthesis.sdc
export VERILOG_INCLUDE_DIRS = $(wildcard $(VSDBabySoC_DIR)/include/)

export ADDITIONAL_LIBS = \
    $(VSDBabySoC_DIR)/lib/avsddac.lib \
    $(VSDBabySoC_DIR)/lib/avsdpll.lib

export ADDITIONAL_GDS  = $(wildcard $(VSDBabySoC_DIR)/gds/*.gds)
export ADDITIONAL_LEFS = $(wildcard $(VSDBabySoC_DIR)/lef/*.lef)

export ADDITIONAL_ROUTING_BLOCKAGES = $(VSDBabySoC_DIR)/route_blockages.tcl

export CLOCK_PORT = CLK
export CLOCK_NET  = CLK

export FP_PIN_ORDER_CFG = $(VSDBabySoC_DIR)/pin_order.cfg

export DIE_AREA  = 0   0   1600 1600
export CORE_AREA = 20  20  1580 1580

export FP_CORE_UTIL    = 40
export PLACE_DENSITY   = 0.30   # Changed from 0.48

export MACRO_PLACE_HALO    = 40 40  # Changed from 20 20
export MACRO_PLACE_CHANNEL = 40 40

export RTLMP_FLOW = 0
export MACRO_PLACEMENT = $(VSDBabySoC_DIR)/macro.cfg

 #dac 320 900 N
 #pll 40  40  N

export CTS_BUF_DISTANCE  = 600
export SKIP_GATE_CLONING = 1

# Normal congestion settings
export GRT_ALLOW_CONGESTION      = 1
export GRT_SKIP_CONGESTION_CHECK = 0
export GRT_CONGESTION_DRIVEN     = 1
export GRT_MAX_ITERATIONS        = 700
export GRT_ADJUSTMENT            = 0.15
export GRT_VIA_COST_ADJUSTMENT   = 3.0
export GRT_LAYER_ADJUSTMENTS     = {met1:0.25,met2:0.20,met3:0.10,met4:0.05,met5:0.00}
export GRT_MAX_TIME              = 3600

# ---- REQUIRED FIXES FOR YOUR VERSION (prevents GRT-0116 stop) ----

export GRT_OVERFLOW_ITERS   = 300

export TNS_END_PERCENT      = 100
export REMOVE_ABC_BUFFERS   = 1
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS    = 1
export GRT_FAIL_ON_OVERFLOW = 0
export RCX_EXTRACT=1
export RCX_CORNER=typical
export RCX_SPEF_OUTPUT=1
export SPEF_OUTPUT_FILE = $(VSDBabySoC_DIR)/vsdbabysoc.spef
```
</details>

<details>
<summary><strong>avsddac.lef</strong></summary>
  
```shell
VERSION 5.7 ;
NOWIREEXTENSIONATPIN ON ;
DIVIDERCHAR "/" ;
BUSBITCHARS "[]" ;

MACRO avsddac
  CLASS BLOCK ;
  FOREIGN avsddac ;
  ORIGIN -136.770 -613.050 ;
  SIZE 1193.570 BY 562.310 ;

  PIN D[9]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1150.220 613.050 1151.250 755.620 ;
      LAYER met1 ;
        RECT 1148.000 608.000 1154.000 613.050 ;
    END
  END D[9]

  PIN D[8]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1117.200 613.050 1117.850 709.180 ;
      LAYER met1 ;
        RECT 1115.000 608.000 1120.000 613.050 ;
    END
  END D[8]

  PIN D[7]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1096.950 613.050 1097.260 619.510 ;
    END
  END D[7]

  PIN D[6]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 1078.220 613.050 1079.660 614.760 ;
    END
  END D[6]

  PIN D[5]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 226.250 1174.740 228.070 1175.360 ;
    END
  END D[5]

  PIN D[4]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 209.700 1174.310 211.150 1175.360 ;
    END
  END D[4]

  PIN D[3]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER met1 ;
        RECT 432.010 1168.710 433.190 1175.360 ;
    END
  END D[3]

  PIN D[2]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 178.380 1174.680 180.110 1175.360 ;
    END
  END D[2]

  PIN D[1]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 160.760 1172.150 162.090 1175.360 ;
    END
  END D[1]

  PIN D[0]
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 145.960 1172.220 146.570 1175.360 ;
    END
  END D[0]

  PIN VPWR
    DIRECTION INOUT ;
    USE POWER ;
    PORT
      LAYER met4 ;
        RECT 152.670 1171.410 154.120 1175.360 ;
    END
  END VPWR

  PIN VGND
    DIRECTION INOUT ;
    USE GROUND ;
    PORT
      LAYER met2 ;
        RECT 136.770 873.200 144.500 873.520 ;
    END
  END VGND

  PIN VREFH
    DIRECTION INPUT ;
    USE SIGNAL ;
    PORT
      LAYER li1 ;
        RECT 142.900 1170.680 143.170 1175.360 ;
    END
  END VREFH

  PIN OUT
    DIRECTION OUTPUT ;
    USE SIGNAL ;
    PORT
      LAYER met4 ;
        RECT 1172.680 1127.710 1173.730 1175.360 ;
    END
  END OUT

  OBS
      LAYER li1 ;
        RECT 136.770 1170.510 142.730 1175.360 ;
        RECT 143.340 1172.050 145.790 1175.360 ;
        RECT 146.740 1172.050 160.590 1175.360 ;
        RECT 143.340 1171.980 160.590 1172.050 ;
        RECT 162.260 1174.510 178.210 1175.360 ;
        RECT 180.280 1174.510 209.530 1175.360 ;
        RECT 162.260 1174.140 209.530 1174.510 ;
        RECT 211.320 1174.570 226.080 1175.360 ;
        RECT 228.240 1174.570 1164.190 1175.360 ;
        RECT 211.320 1174.140 1164.190 1174.570 ;
        RECT 162.260 1171.980 1164.190 1174.140 ;
        RECT 143.340 1170.510 1164.190 1171.980 ;
        RECT 136.770 755.790 1164.190 1170.510 ;
        RECT 136.770 709.350 1150.050 755.790 ;
        RECT 136.770 619.680 1117.030 709.350 ;
        RECT 136.770 614.930 1096.780 619.680 ;
        RECT 136.770 613.050 1078.050 614.930 ;
        RECT 1079.830 613.050 1096.780 614.930 ;
        RECT 1097.430 613.050 1117.030 619.680 ;
        RECT 1118.020 613.050 1150.050 709.350 ;
        RECT 1151.420 613.050 1164.190 755.790 ;
      LAYER met1 ;
        RECT 140.750 1168.430 431.730 1175.360 ;
        RECT 433.470 1168.430 1313.360 1175.360 ;
        RECT 140.750 613.050 1313.360 1168.430 ;
      LAYER met2 ;
        RECT 143.250 873.800 1165.380 1172.180 ;
        RECT 144.780 872.920 1165.380 873.800 ;
        RECT 143.250 641.910 1165.380 872.920 ;
      LAYER met3 ;
        RECT 146.990 647.680 1330.340 1169.670 ;
      LAYER met4 ;
        RECT 147.000 1171.010 152.270 1175.050 ;
        RECT 154.520 1171.010 1168.000 1175.050 ;
        RECT 147.000 1127.310 1172.280 1171.010 ;
        RECT 1178.130 1127.310 1188.200 1175.050 ;
        RECT 147.000 648.540 1188.200 1127.310 ;
  END

END avsddac
END LIBRARY

```

</details>

---

## 9. Conclusion

This routing failure exemplifies a common challenge in mixed-signal SoC design: analog macros created without consideration for digital place-and-route requirements. The fix required understanding both the analog macro's physical structure and the digital router's access requirements. By adding appropriate metal layer access points and clearing obstruction blockages, the design achieved zero routing violations with minimal impact to the original macro functionality.

</details>

---

### SPEF File:

It will get generated automatically after the route but we have to write this command:

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```

![alt text]()
![alt text]()
![alt text]()

---
## 💡 Summary

Week 7 focuses on running the complete RTL to GDSII flow for the VSDBabySoC using OpenROAD-Flow-Scripts on the Sky130HD PDK. The process includes synthesis, floorplanning, placement, and clock tree synthesis, followed by density, structural, and timing validation using the OpenROAD GUI. Key issues such as library parsing errors and placement optimization are resolved along the flow. By the end of the week, a timing-clean and layout-ready design is achieved, fully prepared for routing and final GDS export.

---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>

---
