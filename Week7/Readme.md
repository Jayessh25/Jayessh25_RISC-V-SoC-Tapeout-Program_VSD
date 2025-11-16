# VSD Hardware Design Program

## Floorplan and Placement of VSDBabySoC in OpenROAD

### 📚 Contents
 - [RTL2GDS Flow for VSDBabySoC: Initial Steps](#rtl2gds-flow-for-vsdbabysoc-initial-steps)
    - [Key Components of config.mk](#key-components-of-configmk)
    - [File Structure After Setup](#file-structure-after-setup)
  - [Run Synthesis](#run-synthesis)
    - [Synthesis Netlist](#synthesis-netlist)
    - [Synthesis Log](#synthesis-log)
    - [Synthesis Check](#synthesis-check)
    - [Synthesis Stats](#synthesis-stats)
  - [Run Floorplan](#run-floorplan)
    - [Floorplan Error and Fix](#floorplan-error-and-fix)
    - [Floorplan Result (GUI)](#floorplan-result-gui)
 -  [Run Placement](#run-placement)
    - [Placement Result (GUI)](#placement-result-gui)

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

<details> <summary><strong>config.mk</strong></summary>

```
   # Design and Platform Configuration
   export DESIGN_NICKNAME=vsdbabysoc
   export DESIGN_NAME=vsdbabysoc
   export PLATFORM=sky130hd

  # Design Paths
  export vsdbabysoc_DIR=/home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/sky130hd/$(DESIGN_NICKNAME)

  # Explicitly list Verilog files for synthesis
   export VERILOG_FILES = /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/vsdbabysoc.v \
                         /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/rvmyth.v \
                         /home/jayesshsk/VLSI/OpenROAD-flow-scripts/flow/designs/src/vsdbabysoc/clk_gate.v


  # Include Directory for Verilog Header Files
   export VERILOG_INCLUDE_DIRS = $(vsdbabysoc_DIR)/include

  # Constraints File
    export SDC_FILE = $(vsdbabysoc_DIR)/vsdbabysoc_synthesis.sdc

  # Additional GDS Files
    export ADDITIONAL_GDS = $(vsdbabysoc_DIR)/gds/avsddac.gds \
                            $(vsdbabysoc_DIR)/gds/avsdpll.gds

  # Additional LEF Files
   export ADDITIONAL_LEFS = $(vsdbabysoc_DIR)/lef/avsddac.lef \
                            $(vsdbabysoc_DIR)/lef/avsdpll.lef

  # Additional LIB Files
   export ADDITIONAL_LIBS = $(vsdbabysoc_DIR)/lib/avsddac.lib \
                            $(vsdbabysoc_DIR)/lib/avsdpll.lib

 # Pin Order and Macro Placement Configurations
   export FP_PIN_ORDER_CFG = $(vsdbabysoc_DIR)/pin_order.cfg
   export MACRO_PLACEMENT_CFG = $(vsdbabysoc_DIR)/macro.cfg
  
   
 # Clock Configuration
   export CLOCK_PORT = CLK
   export CLOCK_NET  = $(CLOCK_PORT)
   export CLOCK_PERIOD = 20.0

# Floorplanning Configuration
  export DIE_AREA   = 0 0 1600 1600
  export CORE_AREA  = 20 20 1590 1590
 
# Placement Configuration
  export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600 -exclude right:* -exclude top:* -exclude bottom:*

# Tuning for Timing and Buffers
  export TNS_END_PERCENT     = 100
  export REMOVE_ABC_BUFFERS  = 1
  export CTS_BUF_DISTANCE    = 600
  export SKIP_GATE_CLONING   = 1
  
 # Magic Tool Configuration
   export MAGIC_ZEROIZE_ORIGIN = 0
   export MAGIC_EXT_USE_GDS    = 1
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

   Number of wires:               6551
   Number of wire bits:           6551
   Number of public wires:        1285
   Number of public wire bits:    1285
   Number of ports:                  7
   Number of port bits:              7
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:               6440
     avsddac                         1
     avsdpll                         1
     sky130_fd_sc_hd__a2111o_1       1
     sky130_fd_sc_hd__a2111oi_0      3
     sky130_fd_sc_hd__a2111oi_2      1
     sky130_fd_sc_hd__a211o_1        9
     sky130_fd_sc_hd__a211o_2        1
     sky130_fd_sc_hd__a211oi_1      47
     sky130_fd_sc_hd__a21bo_2        1
     sky130_fd_sc_hd__a21boi_1       1
     sky130_fd_sc_hd__a21o_1        29
     sky130_fd_sc_hd__a21oi_1      816
     sky130_fd_sc_hd__a21oi_2        1
     sky130_fd_sc_hd__a221o_1       10
     sky130_fd_sc_hd__a221oi_1      51
     sky130_fd_sc_hd__a221oi_2       4
     sky130_fd_sc_hd__a22o_1        45
     sky130_fd_sc_hd__a22oi_1      183
     sky130_fd_sc_hd__a22oi_2        1
     sky130_fd_sc_hd__a2bb2oi_1      4
     sky130_fd_sc_hd__a311o_1        3
     sky130_fd_sc_hd__a311o_2        1
     sky130_fd_sc_hd__a311oi_1      37
     sky130_fd_sc_hd__a311oi_2       1
     sky130_fd_sc_hd__a31o_2        16
     sky130_fd_sc_hd__a31oi_1       44
     sky130_fd_sc_hd__a31oi_2        1
     sky130_fd_sc_hd__a32o_1         2
     sky130_fd_sc_hd__a32oi_1        5
     sky130_fd_sc_hd__a41o_1         4
     sky130_fd_sc_hd__a41oi_1        3
     sky130_fd_sc_hd__a41oi_2        2
     sky130_fd_sc_hd__and2_0         2
     sky130_fd_sc_hd__and2_1        22
     sky130_fd_sc_hd__and3_1        38
     sky130_fd_sc_hd__and4_1         3
     sky130_fd_sc_hd__buf_1         38
     sky130_fd_sc_hd__buf_2         35
     sky130_fd_sc_hd__buf_4          8
     sky130_fd_sc_hd__buf_6          7
     sky130_fd_sc_hd__buf_8          2
     sky130_fd_sc_hd__clkbuf_1     502
     sky130_fd_sc_hd__clkbuf_2       7
     sky130_fd_sc_hd__clkbuf_8       1
     sky130_fd_sc_hd__clkinv_1       6
     sky130_fd_sc_hd__conb_1         1
     sky130_fd_sc_hd__dfxtp_1     1144
     sky130_fd_sc_hd__fa_1           4
     sky130_fd_sc_hd__ha_1         101
     sky130_fd_sc_hd__inv_1         94
     sky130_fd_sc_hd__inv_2          1
     sky130_fd_sc_hd__mux2_2        39
     sky130_fd_sc_hd__mux2i_1       76
     sky130_fd_sc_hd__mux2i_2        3
     sky130_fd_sc_hd__mux2i_4        4
     sky130_fd_sc_hd__mux4_1        12
     sky130_fd_sc_hd__mux4_2        79
     sky130_fd_sc_hd__nand2_1     1371
     sky130_fd_sc_hd__nand2b_1      24
     sky130_fd_sc_hd__nand3_1      228
     sky130_fd_sc_hd__nand3b_1      37
     sky130_fd_sc_hd__nand4_1       51
     sky130_fd_sc_hd__nand4b_1       1
     sky130_fd_sc_hd__nor2_1       264
     sky130_fd_sc_hd__nor2b_1       54
     sky130_fd_sc_hd__nor3_1        52
     sky130_fd_sc_hd__nor3b_1        7
     sky130_fd_sc_hd__nor4_1        20
     sky130_fd_sc_hd__o2111ai_1      1
     sky130_fd_sc_hd__o211a_1        1
     sky130_fd_sc_hd__o211ai_1      48
     sky130_fd_sc_hd__o211ai_2       4
     sky130_fd_sc_hd__o21a_1        28
     sky130_fd_sc_hd__o21ai_0      385
     sky130_fd_sc_hd__o21ai_1       22
     sky130_fd_sc_hd__o21ai_2        9
     sky130_fd_sc_hd__o21ba_2        2
     sky130_fd_sc_hd__o21bai_1       8
     sky130_fd_sc_hd__o221a_2        2
     sky130_fd_sc_hd__o221ai_1      19
     sky130_fd_sc_hd__o22a_1        33
     sky130_fd_sc_hd__o22ai_1       17
     sky130_fd_sc_hd__o2bb2ai_1      6
     sky130_fd_sc_hd__o311a_1        3
     sky130_fd_sc_hd__o311ai_0       4
     sky130_fd_sc_hd__o311ai_1       2
     sky130_fd_sc_hd__o31a_1         7
     sky130_fd_sc_hd__o31ai_1       34
     sky130_fd_sc_hd__o31ai_4        1
     sky130_fd_sc_hd__o32a_1         2
     sky130_fd_sc_hd__o32ai_1        3
     sky130_fd_sc_hd__o41ai_1        4
     sky130_fd_sc_hd__o41ai_2        1
     sky130_fd_sc_hd__or2_1          1
     sky130_fd_sc_hd__or2_2          8
     sky130_fd_sc_hd__or3_1         20
     sky130_fd_sc_hd__or3b_1         2
     sky130_fd_sc_hd__or3b_2         2
     sky130_fd_sc_hd__or4_1          9
     sky130_fd_sc_hd__or4b_2         1
     sky130_fd_sc_hd__xnor2_1       57
     sky130_fd_sc_hd__xor2_1        27

   Area for cell type \avsddac is unknown!
   Area for cell type \avsdpll is unknown!

   Chip area for module '\vsdbabysoc': 52933.267200
     of which used for sequential elements: 22901.964800 (43.27%)
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

![Alt Text]()

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
worst slack max 6.63

==========================================================================
cts final report_clock_skew
--------------------------------------------------------------------------
Clock clk
   1.11 source latency core.CPU_result_a4[0]$_DFF_P_/CLK ^
  -0.89 target latency core.CPU_Dmem_value_a5[2][21]$_SDFFE_PP0P_/CLK ^
   0.00 CRPR
--------------
   0.22 setup skew


==========================================================================
cts final report_checks -path_delay min
--------------------------------------------------------------------------
Startpoint: core.CPU_src2_value_a3[26]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a4[26]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.32    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.00    0.56 ^ clkbuf_5_24__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.13    0.15    0.35    0.91 ^ clkbuf_5_24__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_24__leaf_CLK (net)
                  0.15    0.00    0.91 ^ core.CPU_src2_value_a3[26]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     3    0.02    0.11    0.40    1.32 v core.CPU_src2_value_a3[26]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_src2_value_a3[26] (net)
                  0.11    0.00    1.32 v core.CPU_src2_value_a4[26]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  1.32   data arrival time

                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.32    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.00    0.56 ^ clkbuf_5_26__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    10    0.14    0.16    0.35    0.92 ^ clkbuf_5_26__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_26__leaf_CLK (net)
                  0.16    0.00    0.92 ^ clkbuf_leaf_92_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.03    0.05    0.17    1.09 ^ clkbuf_leaf_92_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_92_CLK (net)
                  0.05    0.00    1.09 ^ core.CPU_src2_value_a4[26]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00    1.09   clock reconvergence pessimism
                         -0.08    1.01   library hold time
                                  1.01   data required time
-----------------------------------------------------------------------------
                                  1.01   data required time
                                 -1.32   data arrival time
-----------------------------------------------------------------------------
                                  0.31   slack (MET)



==========================================================================
cts final report_checks -path_delay max
--------------------------------------------------------------------------
Startpoint: core.CPU_is_slli_a3$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.32    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.01    0.57 ^ clkbuf_5_11__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.14    0.16    0.36    0.93 ^ clkbuf_5_11__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_11__leaf_CLK (net)
                  0.16    0.00    0.93 ^ clkbuf_leaf_77_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     5    0.02    0.04    0.16    1.09 ^ clkbuf_leaf_77_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_77_CLK (net)
                  0.04    0.00    1.09 ^ core.CPU_is_slli_a3$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
    10    0.06    0.58    0.68    1.78 ^ core.CPU_is_slli_a3$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_is_slli_a3 (net)
                  0.58    0.00    1.78 ^ place540/A (sky130_fd_sc_hd__buf_1)
     8    0.08    0.87    0.72    2.50 ^ place540/X (sky130_fd_sc_hd__buf_1)
                                         net544 (net)
                  0.87    0.00    2.50 ^ _07555_/A (sky130_fd_sc_hd__nand2_2)
     1    0.01    0.18    0.16    2.66 v _07555_/Y (sky130_fd_sc_hd__nand2_2)
                                         _02538_ (net)
                  0.18    0.00    2.66 v _07556_/B1 (sky130_fd_sc_hd__o21ai_4)
    18    0.09    0.62    0.29    2.95 ^ _07556_/Y (sky130_fd_sc_hd__o21ai_4)
                                         _02539_ (net)
                  0.62    0.00    2.95 ^ place456/A (sky130_fd_sc_hd__buf_1)
    11    0.06    0.64    0.57    3.52 ^ place456/X (sky130_fd_sc_hd__buf_1)
                                         net460 (net)
                  0.64    0.00    3.52 ^ _07557_/C (sky130_fd_sc_hd__nor3_4)
     5    0.03    0.15    0.13    3.65 v _07557_/Y (sky130_fd_sc_hd__nor3_4)
                                         _02540_ (net)
                  0.15    0.00    3.65 v _08630_/A1 (sky130_fd_sc_hd__a22oi_1)
     1    0.01    0.24    0.27    3.92 ^ _08630_/Y (sky130_fd_sc_hd__a22oi_1)
                                         _03589_ (net)
                  0.24    0.00    3.92 ^ _08631_/B1 (sky130_fd_sc_hd__o311ai_2)
     1    0.01    0.17    0.19    4.11 v _08631_/Y (sky130_fd_sc_hd__o311ai_2)
                                         _03590_ (net)
                  0.17    0.00    4.11 v _08635_/C1 (sky130_fd_sc_hd__a2111o_1)
     1    0.01    0.09    0.42    4.53 v _08635_/X (sky130_fd_sc_hd__a2111o_1)
                                         _03594_ (net)
                  0.09    0.00    4.53 v _08643_/A (sky130_fd_sc_hd__and3_4)
     2    0.01    0.04    0.18    4.71 v _08643_/X (sky130_fd_sc_hd__and3_4)
                                         _03602_ (net)
                  0.04    0.00    4.71 v _08661_/A1 (sky130_fd_sc_hd__a21oi_4)
    11    0.07    0.51    0.43    5.14 ^ _08661_/Y (sky130_fd_sc_hd__a21oi_4)
                                         _03620_ (net)
                  0.51    0.01    5.15 ^ _09042_/A2 (sky130_fd_sc_hd__o31ai_1)
     1    0.00    0.09    0.14    5.29 v _09042_/Y (sky130_fd_sc_hd__o31ai_1)
                                         _00847_ (net)
                  0.09    0.00    5.29 v core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.29   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.32    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03   11.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54   11.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.01   11.57 ^ clkbuf_5_1__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.11    0.13    0.33   11.90 ^ clkbuf_5_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_1__leaf_CLK (net)
                  0.13    0.00   11.90 ^ clkbuf_leaf_238_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.02    0.04    0.15   12.05 ^ clkbuf_leaf_238_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_238_CLK (net)
                  0.04    0.00   12.05 ^ core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   12.05   clock reconvergence pessimism
                         -0.13   11.92   library setup time
                                 11.92   data required time
-----------------------------------------------------------------------------
                                 11.92   data required time
                                 -5.29   data arrival time
-----------------------------------------------------------------------------
                                  6.63   slack (MET)



==========================================================================
cts final report_checks -unconstrained
--------------------------------------------------------------------------
Startpoint: core.CPU_is_slli_a3$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.32    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.01    0.57 ^ clkbuf_5_11__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     9    0.14    0.16    0.36    0.93 ^ clkbuf_5_11__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_11__leaf_CLK (net)
                  0.16    0.00    0.93 ^ clkbuf_leaf_77_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     5    0.02    0.04    0.16    1.09 ^ clkbuf_leaf_77_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_77_CLK (net)
                  0.04    0.00    1.09 ^ core.CPU_is_slli_a3$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
    10    0.06    0.58    0.68    1.78 ^ core.CPU_is_slli_a3$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_is_slli_a3 (net)
                  0.58    0.00    1.78 ^ place540/A (sky130_fd_sc_hd__buf_1)
     8    0.08    0.87    0.72    2.50 ^ place540/X (sky130_fd_sc_hd__buf_1)
                                         net544 (net)
                  0.87    0.00    2.50 ^ _07555_/A (sky130_fd_sc_hd__nand2_2)
     1    0.01    0.18    0.16    2.66 v _07555_/Y (sky130_fd_sc_hd__nand2_2)
                                         _02538_ (net)
                  0.18    0.00    2.66 v _07556_/B1 (sky130_fd_sc_hd__o21ai_4)
    18    0.09    0.62    0.29    2.95 ^ _07556_/Y (sky130_fd_sc_hd__o21ai_4)
                                         _02539_ (net)
                  0.62    0.00    2.95 ^ place456/A (sky130_fd_sc_hd__buf_1)
    11    0.06    0.64    0.57    3.52 ^ place456/X (sky130_fd_sc_hd__buf_1)
                                         net460 (net)
                  0.64    0.00    3.52 ^ _07557_/C (sky130_fd_sc_hd__nor3_4)
     5    0.03    0.15    0.13    3.65 v _07557_/Y (sky130_fd_sc_hd__nor3_4)
                                         _02540_ (net)
                  0.15    0.00    3.65 v _08630_/A1 (sky130_fd_sc_hd__a22oi_1)
     1    0.01    0.24    0.27    3.92 ^ _08630_/Y (sky130_fd_sc_hd__a22oi_1)
                                         _03589_ (net)
                  0.24    0.00    3.92 ^ _08631_/B1 (sky130_fd_sc_hd__o311ai_2)
     1    0.01    0.17    0.19    4.11 v _08631_/Y (sky130_fd_sc_hd__o311ai_2)
                                         _03590_ (net)
                  0.17    0.00    4.11 v _08635_/C1 (sky130_fd_sc_hd__a2111o_1)
     1    0.01    0.09    0.42    4.53 v _08635_/X (sky130_fd_sc_hd__a2111o_1)
                                         _03594_ (net)
                  0.09    0.00    4.53 v _08643_/A (sky130_fd_sc_hd__and3_4)
     2    0.01    0.04    0.18    4.71 v _08643_/X (sky130_fd_sc_hd__and3_4)
                                         _03602_ (net)
                  0.04    0.00    4.71 v _08661_/A1 (sky130_fd_sc_hd__a21oi_4)
    11    0.07    0.51    0.43    5.14 ^ _08661_/Y (sky130_fd_sc_hd__a21oi_4)
                                         _03620_ (net)
                  0.51    0.01    5.15 ^ _09042_/A2 (sky130_fd_sc_hd__o31ai_1)
     1    0.00    0.09    0.14    5.29 v _09042_/Y (sky130_fd_sc_hd__o31ai_1)
                                         _00847_ (net)
                  0.09    0.00    5.29 v core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  5.29   data arrival time

                         11.00   11.00   clock clk (rise edge)
                          0.00   11.00   clock source latency
     1    0.32    0.00    0.00   11.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.05    0.03   11.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    32    0.62    0.61    0.54   11.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.61    0.01   11.57 ^ clkbuf_5_1__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.11    0.13    0.33   11.90 ^ clkbuf_5_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_5_1__leaf_CLK (net)
                  0.13    0.00   11.90 ^ clkbuf_leaf_238_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     6    0.02    0.04    0.15   12.05 ^ clkbuf_leaf_238_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_238_CLK (net)
                  0.04    0.00   12.05 ^ core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.00   12.05   clock reconvergence pessimism
                         -0.13   11.92   library setup time
                                 11.92   data required time
-----------------------------------------------------------------------------
                                 11.92   data required time
                                 -5.29   data arrival time
-----------------------------------------------------------------------------
                                  6.63   slack (MET)



==========================================================================
cts final report_check_types -max_slew -max_cap -max_fanout -violators
--------------------------------------------------------------------------

==========================================================================
cts final max_slew_check_slack
--------------------------------------------------------------------------
0.07318645715713501

==========================================================================
cts final max_slew_check_limit
--------------------------------------------------------------------------
1.4979510307312012

==========================================================================
cts final max_slew_check_slack_limit
--------------------------------------------------------------------------
0.0489

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
0.006848945748060942

==========================================================================
cts final max_capacitance_check_limit
--------------------------------------------------------------------------
0.1620579957962036

==========================================================================
cts final max_capacitance_check_slack_limit
--------------------------------------------------------------------------
0.0423

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
Startpoint: core.CPU_is_slli_a3$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.56    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.37    0.93 ^ clkbuf_5_11__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.16    1.09 ^ clkbuf_leaf_77_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    1.09 ^ core.CPU_is_slli_a3$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.68    1.78 ^ core.CPU_is_slli_a3$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.72    2.50 ^ place540/X (sky130_fd_sc_hd__buf_1)
   0.16    2.66 v _07555_/Y (sky130_fd_sc_hd__nand2_2)
   0.29    2.95 ^ _07556_/Y (sky130_fd_sc_hd__o21ai_4)
   0.57    3.52 ^ place456/X (sky130_fd_sc_hd__buf_1)
   0.13    3.65 v _07557_/Y (sky130_fd_sc_hd__nor3_4)
   0.27    3.92 ^ _08630_/Y (sky130_fd_sc_hd__a22oi_1)
   0.19    4.11 v _08631_/Y (sky130_fd_sc_hd__o311ai_2)
   0.42    4.53 v _08635_/X (sky130_fd_sc_hd__a2111o_1)
   0.18    4.71 v _08643_/X (sky130_fd_sc_hd__and3_4)
   0.43    5.14 ^ _08661_/Y (sky130_fd_sc_hd__a21oi_4)
   0.15    5.29 v _09042_/Y (sky130_fd_sc_hd__o31ai_1)
   0.00    5.29 v core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
           5.29   data arrival time

  11.00   11.00   clock clk (rise edge)
   0.00   11.00   clock source latency
   0.00   11.00 ^ pll/CLK (avsdpll)
   0.56   11.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.34   11.90 ^ clkbuf_5_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.15   12.05 ^ clkbuf_leaf_238_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00   12.05 ^ core.CPU_Xreg_value_a4[16][30]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00   12.05   clock reconvergence pessimism
  -0.13   11.92   library setup time
          11.92   data required time
---------------------------------------------------------
          11.92   data required time
          -5.29   data arrival time
---------------------------------------------------------
           6.63   slack (MET)



==========================================================================
cts final report_checks -path_delay min reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_src2_value_a3[26]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a4[26]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.56    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.35    0.91 ^ clkbuf_5_24__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.91 ^ core.CPU_src2_value_a3[26]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.40    1.32 v core.CPU_src2_value_a3[26]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.00    1.32 v core.CPU_src2_value_a4[26]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           1.32   data arrival time

   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.56    0.56 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.36    0.92 ^ clkbuf_5_26__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.17    1.09 ^ clkbuf_leaf_92_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    1.09 ^ core.CPU_src2_value_a4[26]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.00    1.09   clock reconvergence pessimism
  -0.08    1.01   library hold time
           1.01   data required time
---------------------------------------------------------
           1.01   data required time
          -1.32   data arrival time
---------------------------------------------------------
           0.31   slack (MET)



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
5.2882

==========================================================================
cts final critical path slack
--------------------------------------------------------------------------
6.6316

==========================================================================
cts final slack div critical path delay
--------------------------------------------------------------------------
125.403729

==========================================================================
cts final report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             4.41e-03   5.76e-04   9.26e-09   4.99e-03  29.3%
Combinational          1.32e-03   2.37e-03   1.04e-08   3.69e-03  21.7%
Clock                  5.54e-03   2.78e-03   4.76e-09   8.32e-03  49.0%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  1.13e-02   5.72e-03   2.44e-08   1.70e-02 100.0%
                          66.3%      33.7%       0.0%
```
</details>


### `run routing`

```shell
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk route
```

![Alt Text]()

![Alt Text]()


