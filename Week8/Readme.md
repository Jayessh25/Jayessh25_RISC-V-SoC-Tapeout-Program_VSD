# 🚀 Week 8 : – Post-Routing STA analysis of VSDBabySoC Design

<div align="center">

![VLSI](https://img.shields.io/badge/VLSI-System%20Design-blue?style=for-the-badge&logo=chip)
![Day](https://img.shields.io/badge/Week-8-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

Wlecome to **Week8** –   is to perform a complete **post-routing Static Timing Analysis (STA)** of the **VSDBabySoC design using OpenSTA** across multiple **PVT corners**. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. 

---

## 📑 Table of Contents

1. [🎯 Objective](#-objective)
2. [📝 Summary](#-summary)
3. [📂 Key Files Used for STA](#-key-files)
4. [📜 sta_across_pvt_route.tcl Script](#sta_across_pvt_routetcl-file)
5. [🕒 Post-CTS SDC File](#vsdbabysoc_post_ctssdc-file)
6. [📡 SPEF (Post-Route Parasitics)](#post-route-spef-file)
7. [🚀 Running STA Using Docker](#running-sta)
8. [📄 STA Output Files Generated](#sta-output-files)
9. [📊 Post-Synthesis Timing Summary](#post-synthesis-results-summary)
10. [📊 Post-Route Timing Summary](#post-routing-results-summary)
11. [📈 Comparison Graphs](#comparison-graphs)
12. [🔍 Key Differences: Post-Synthesis vs Post-Route](#key-differences-post-synthesis-vs-post-route-timing-analysis)
13. [📝 Final Summary](#summary)
14. [📚 Repository & Author](#repository--author)

---

### 1.Objective
The objective of Week 8 is to perform a complete post-routing Static Timing Analysis (STA) of the VSDBabySoC design using OpenSTA across multiple PVT corners. This includes running STA with accurate post-route parasitic data (SPEF), post-CTS constraints (SDC), and multi-corner Liberty files to evaluate worst-case setup, hold, WNS, and TNS metrics. The goal is to verify the timing integrity of the design after routing, identify corner-specific violations, and ensure sign-off-quality timing closure for the final GDSII.

---
### `Key Files`

To perform reliable timing verification of the BabySoC design after routing, we use OpenSTA with a dedicated TCL script, a post-CTS constraints file, and the post-route SPEF file.

The `sta_across_pvt_route.tcl` script automates static timing analysis across multiple process, voltage, and temperature (PVT) corners, while the `vsdbabysoc_post_cts.sdc` file provides the design-specific timing constraints generated after clock tree synthesis, and the `vsdbabysoc.spef` file supplies the post-route parasitic RC data. Together, these files ensure that STA is run under the correct operating conditions with accurate parasitic delays, and that reports such as setup/hold slack, WNS, and TNS are captured for each library corner.

## sta_across_pvt_route.tcl File

```
 set list_of_lib_files(1) "sky130_fd_sc_hd__tt_025C_1v80.lib"
set list_of_lib_files(2) "sky130_fd_sc_hd__ff_100C_1v65.lib"
set list_of_lib_files(3) "sky130_fd_sc_hd__ff_100C_1v95.lib"
set list_of_lib_files(4) "sky130_fd_sc_hd__ff_n40C_1v56.lib"
set list_of_lib_files(5) "sky130_fd_sc_hd__ff_n40C_1v65.lib"
set list_of_lib_files(6) "sky130_fd_sc_hd__ff_n40C_1v76.lib"
set list_of_lib_files(7) "sky130_fd_sc_hd__ss_100C_1v40.lib"
set list_of_lib_files(8) "sky130_fd_sc_hd__ss_100C_1v60.lib"
set list_of_lib_files(9) "sky130_fd_sc_hd__ss_n40C_1v28.lib"
set list_of_lib_files(10) "sky130_fd_sc_hd__ss_n40C_1v35.lib"
set list_of_lib_files(11) "sky130_fd_sc_hd__ss_n40C_1v40.lib"
set list_of_lib_files(12) "sky130_fd_sc_hd__ss_n40C_1v44.lib"
set list_of_lib_files(13) "sky130_fd_sc_hd__ss_n40C_1v76.lib"

# Extra liberty files
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsdpll.lib
read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/avsddac.lib

for {set i 1} {$i <= [array size list_of_lib_files]} {incr i} {

    read_liberty /data/VLSI/VSDBabySoc/OpenSTA/examples/timing_libs/$list_of_lib_files($i)

    read_verilog /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.v

    link_design vsdbabysoc
    current_design

    # FIXED SDC PATH
    read_sdc /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/5_route.sdc

    read_spef /data/VLSI/OpenROAD-flow-scripts/flow/results/sky130hd/vsdbabysoc/base/6_final.spef

    check_setup -verbose

    report_checks -path_delay min_max -fields {nets cap slew input_pins fanout} -digits {4} \
      > /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/min_max_$list_of_lib_files($i).txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt
    report_worst_slack -max -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_max_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt
    report_worst_slack -min -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_worst_min_slack.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt
    report_tns -digits {4} \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_tns.txt

    exec echo "$list_of_lib_files($i)" \
      >> /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
    report_wns -digits {4} \
      >> /data/VLSI/VSDBabySoC/OpenSTA/examples/BabySoC/STA_OUTPUT/route/sta_wns.txt
}

```
</details>

This `vsdbabysoc_post_cts.sdc` file is an auto-generated SDC created after clock tree synthesis. It sets the current design to `vsdbabysoc` and defines the basic timing environment. The file specifies a clock named `clk` with an `11 ns` period, driven from the pin `pll/CLK`, and marks it as a propagated clock for STA. Sections for environment and design rules are also included for adding further constraints if needed.



### `Running STA`

To run the post-route STA using Docker, follow these steps to execute the `sta_across_pvt_route.tcl` script. Launch a Docker container with your local directory mounted, run the script inside the container, and it will generate all timing reports such as setup/hold slack, WNS, and TNS in the mounted `/data` folder. Using Docker ensures a consistent and reproducible environment for performing the analysis.

```shell
docker run -it -v $HOME:/data opensta /data/VLSI/VSDBabySoc/OpenSTA/examples/BabySoC/sta_across_pvt_route.tcl
```

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command1.png)

After running the STA script, you can navigate to the `STA_OUTPUT/route/` directory to see all the generated timing reports. This includes detailed path delay reports for each library corner (`min_max_*.txt`), worst setup and hold slack summaries (`sta_worst_max_slack.txt and sta_worst_min_slack.txt`), as well as total negative slack (sta_tns.txt) and worst negative slack (`sta_wns.txt`). These files provide a complete overview of the BabySoC design’s timing performance after routing.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command2.png)

### `Post-Synthesis Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command4.png)

### `Post-routing Results Summary`

Here is a tabulated view of the key timing results generated by the STA script.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command3.png)

### 📈 `Comparison Graphs`

Graph created through Google colab

```
import pandas as pd
import matplotlib.pyplot as plt
from io import StringIO

# -----------------------------
# 1. POST-SYNTHESIS DATA 
# -----------------------------
post_synth_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
sky130_fd_sc_hd_tt_025C_1v80	0.8595	0.3096	0	0
sky130_fd_sc_hd_ff_100C_1v65	2.2764	0.2491	0	0
sky130_fd_sc_hd_ff_100C_1v95	3.7138	0.196	0	0
sky130_fd_sc_hd_ff_n40C_1v56	0.8214	0.2915	0	0
sky130_fd_sc_hd_ff_n40C_1v65	1.8597	0.2551	0	0
sky130_fd_sc_hd_ff_n40C_1v76	2.7707	0.2243	0	0
sky130_fd_sc_hd_ss_100C_1v40	-13.6381	0.9053	-13.6381	-7567.7964
sky130_fd_sc_hd_ss_100C_1v60	-6.7098	0.642	-6.7098	-2833.05
sky130_fd_sc_hd_ss_n40C_1v28	-51.2061	1.8296	-51.2061	-36861.4102
sky130_fd_sc_hd_ss_n40C_1v35	-32.0887	1.3475	-32.0887	-23317.6035
sky130_fd_sc_hd_ss_n40C_1v40	-23.829	1.1249	-23.829	-17211.252
sky130_fd_sc_hd_ss_n40C_1v44	-19.201	0.9909	-19.201	-13652.0469
sky130_fd_sc_hd_ss_n40C_1v76	-4.4548	0.5038	-4.4548	-1842.5518
"""

df_synth = pd.read_csv(StringIO(post_synth_data), sep="\t")
df_synth["Corner"] = df_synth["PVT_CORNER"].str.replace("sky130_fd_sc_hd_", "")


# -------------------------------------------------
# 2. POST-ROUTE DATA 
# -------------------------------------------------
post_route_data = """PVT_CORNER	WORST SETUP SLACK	WORST HOLD SLACK	WNS	TNS
tt_025C_1v80	6.5987	0.3104	0	0
ff_100C_1v65	7.5661	0.2516	0	0
ff_100C_1v95	3.3245	0.1977	0	0
ff_n40C_1v56	6.5235	0.2904	0	0
ff_n40C_1v65	7.1347	0.2572	0	0
ff_n40C_1v76	7.6698	0.2267	0	0
ss_100C_1v40	-1.4062	0.8794	-1.4062	-132.1887
ss_100C_1v60	2.5442	0.6267	0	0
ss_n40C_1v28	-25.0572	1.6106	-25.0572	-14950.0332
ss_n40C_1v35	-13.879	1.2095	-13.879	-7197.1201
ss_n40C_1v40	-9.2025	1.0153	-9.2025	-3827.5981
ss_n40C_1v44	-6.4161	0.9072	-6.4161	-2041.2456
ss_n40C_1v76	3.2898	0.4688	0	0
"""

df_route = pd.read_csv(StringIO(post_route_data), sep="\t")
df_route["Corner"] = df_route["PVT_CORNER"]


# -----------------------------
# 3. PLOTTING COMPARISON
# -----------------------------
metrics = {
    "WORST SETUP SLACK": "Worst Setup Slack",
    "WORST HOLD SLACK": "Worst Hold Slack",
    "WNS": "Worst Negative Slack",
    "TNS": "Total Negative Slack"
}

for metric, title in metrics.items():
    plt.figure(figsize=(14, 6))
    
    plt.plot(df_synth["Corner"], df_synth[metric], label="Post-Synthesis", marker='o', linewidth=2)
    plt.plot(df_route["Corner"], df_route[metric], label="Post-Route", marker='s', linewidth=2)
    
    plt.axhline(0, color='red', linestyle='--', linewidth=1.5)
    
    plt.title(f"{title} Comparison: Post-Synthesis vs Post-Route", fontsize=16)
    plt.xlabel("PVT Corner")
    plt.ylabel(f"{title} (ns)")
    plt.xticks(rotation=45, ha='right')
    plt.grid(axis='y', linestyle='--', alpha=0.6)
    plt.legend()
    plt.tight_layout()
    
    plt.show()

```

Here is a graph showing the comparison of `worst-case hold slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command6.png)

Here is a graph showing the comparison of `worst-case setup slack` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command5.png)

Here is a graph showing the comparison of `WNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command7.png)

Here is a graph showing the comparison of `TNS` post-synthesis vs post-routing for the BabySoC design.

![Alt Text](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD/blob/main/Week8/Images/Command8.png)

---

### `Key Differences: Post-Synthesis vs Post-Route Timing Analysis`

| Aspect             | Post-Synthesis Analysis                            | Post-Route Analysis                                           |
| ------------------ | -------------------------------------------------- | ------------------------------------------------------------- |
| **Timing Model**   | Wire-load models (fanout/cell-based estimation)    | Extracted parasitics (RC) from routed layout                  |
| **Clock Network**  | Ideal clock, zero skew, no latency                 | Real clock tree with buffer delays, skew, and insertion delay |
| **Interconnect**   | Delay estimated from fanout-based lookup tables    | Delay calculated from actual metal routing and vias           |
| **Accuracy**       | \~70–80% correlation with sign-off                 | \~95–98% correlation with sign-off                            |
| **Critical Paths** | Critical paths may differ due to estimation errors | Matches actual layout critical paths   |

---

### `Summary`
- Post-synthesis analysis serves as an **early timing checkpoint**.  
- Post-route analysis represents the **golden reference for timing sign-off**.  
- Transition from estimated to actual physical parameters often reveals:
  - New critical paths revealed
  - Realistic clock tree effects (skew, latency)
  - Interconnect-dominated delays
  - Impact of physical proximity & coupling


---

<div align="center">

**📂 Repository:** [Jayessh25_RISC-V-SoC-Tapeout-Program_VSD](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)  
**👨‍💻 Author:** [Jayessh25](https://github.com/Jayessh25)  
**📚 Program:** VLSI System Design (VSD)

[![Follow](https://img.shields.io/github/followers/Jayessh25?style=social)](https://github.com/Jayessh25)
[![Stars](https://img.shields.io/github/stars/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD?style=social)](https://github.com/Jayessh25/Jayessh25_RISC-V-SoC-Tapeout-Program_VSD)

</div>


